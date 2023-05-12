# Add new application

This guide covers the step-by-step guide to onboard a new project/application/microservice on SAAP.

Changes required in application repository:

1. Add Dockerfile to application repository.
1. Push Docker Image to Nexus Docker Repository.
1. Add Helm Chart to application repository.
1. Push Helm Chart to Nexus Helm Repository.

Changes required in `gitops config repository`:

1. Add an environment in `apps-gitops-config` repository for application.
1. Add application helm chart to the dev environment and sync ArgoCD Application.
1. View Application in Cluster.

Replace angle brackets with following values in below templates:

- `<tenant>`: Name of the tenant
- `<application>`: Name of the application
- `<env>`:  Environment name
- `<gitops-repo>`:  URL of your GitOps repo
- `<nexus-repo>`: URL of nexus repository

In this section, we will use [`stakater-nordmart-review-ui`](https://github.com/stakater-lab/stakater-nordmart-review-ui) application as an example and add it to our GitOps structure we made in the previous section.

## Prerequisites

- `tenant` for application must be defined via `infra-gitops-config`
- `tenant` for application should be onboarded onto `apps-gitops-config`
- [helm](https://helm.sh/docs/intro/install/)
- [git](https://git-scm.com/downloads)
- [oc](https://docs.openshift.com/container-platform/4.11/cli_reference/openshift_cli/getting-started-cli.html)
- [buildah](https://github.com/containers/buildah/blob/main/install.md)

## Docker Image and Helm Chart Repository hosted by Nexus

> Ask admin for Docker and Helm Registry Credentials for pushing container images and helm chart respectively.

Navigate to the cluster Forecastle, search `nexus` using the search bar on top menu and copy the nexus url.

- `nexus-docker-reg-url`: Remove `https://` from the start and add `-docker` in URL after `nexus`. This URL points to Docker Registry referred as `nexus-docker-reg-url` in this tutorial for example `nexus-docker-stakater-nexus.apps.clustername.random123string.kubeapp.cloud`.

- `nexus-helm-reg-url` : Remove `https://` from the start, add `-helm` in URL after `nexus` and append `/repository/helm-charts/`. This URL points to Helm Registry referred as `nexus-helm-reg-url` in this tutorial for example `nexus-helm-stakater-nexus.apps.clustername.random123string.kubeapp.cloud/repository/helm-charts/`

  ![nexus-Forecastle](./images/nexus-forecastle.png)

### Login to Docker Registry

Run following command to log into the registry.

```sh
buildah login <nexus-docker-reg-url>
```

Specify admin provided username and password to login.

## 1. Add **Dockerfile** to application repository

We need a **Dockerfile** for our application present inside our code repo to build a container image.  Navigate to [`RedHat image registry`](https://catalog.redhat.com/software/containers/search) and find a suitable base image for the application.

Below is a Dockerfile for a ReactJS application for product reviews. Visit for more info: <https://github.com/stakater-lab/stakater-nordmart-review-ui>

```Dockerfile
FROM node:14 as builder
LABEL name="Nordmart review"

# set workdir
RUN mkdir -p $HOME/application
WORKDIR $HOME/application

# copy the entire application
COPY . .

# install dependencies
RUN npm ci
ARG VERSION

# build the application
RUN npm run build -- --env VERSION=$VERSION

EXPOSE 4200

CMD ["node", "server.js"]
```

> Create [multi-stage builds](https://docs.docker.com/build/building/multi-stage/), use multiple `FROM` statements. Each `FROM` instruction can use a different base, and each of them begins a new stage of the build. You can selectively copy artifacts from one stage to another, leaving behind everything you don’t want in the final image. The end result is the same tiny production image as before, with a significant reduction in complexity. You don’t need to create any intermediate images, and you don’t need to extract any artifacts to your local system at all.

Look into the following dockerizing guides for a start.

| Framework/Language | Reference                                                   |
|--------------------|-------------------------------------------------------------|
| NodeJS             | <https://nodejs.org/en/docs/guides/nodejs-docker-webapp/>     |
| Django             | <https://blog.logrocket.com/dockerizing-django-app/>          |
| General            | <https://www.redhat.com/sysadmin/containerizing-applications> |

## 2. Push Docker Image to Nexus

Lets clone the [`stakater-nordmart-review-ui`](https://github.com/stakater-lab/stakater-nordmart-review-ui) application.

```bash
git clone https://github.com/stakater-lab/stakater-nordmart-review-ui
cd stakater-nordmart-review-ui
```

Replace the placeholders and Run the following command inside application folder.

```sh
# Buldah Bud Info : https://manpages.ubuntu.com/manpages/impish/man1/buildah-bud.1.html
buildah bud --format=docker --tls-verify=false --no-cache -f ./Dockerfile -t <nexus-docker-reg-url>/<tenant-name>/<app-name>:1.0.0 .
```

Lets push the image to nexus docker repo. Make sure to get credentials from Stakater Admin.

```sh
# Buildah push Info https://manpages.ubuntu.com/manpages/impish/man1/buildah-push.1.html
buildah push <nexus-docker-reg-url>/<tenant-name>/stakater-nordmart-review:1.0.0 docker://<nexus-docker-reg-url>/<tenant-name>/stakater-nordmart-review:1.0.0
```

## 3. Add Helm Chart to application repository

In application repo add Helm Chart in ***deploy*** folder at the root of your repository. To configure Helm chart add following 2 files in ***deploy*** folder.

1. A Chart.yaml is YAML file containing information about the chart. We will be using an external helm dependency chart called [Stakater Application Chart](https://github.com/stakater/application). The Helm chart is present in a remote Helm Chart repository

    > More Info : Stakater Application Chart <https://github.com/stakater/application>

    ```yaml
      apiVersion: v2
      # Replace Chart Name with App Name.
      name: stakater-nordmart-review-ui
      description: A Helm chart for Kubernetes
      dependencies:
      - name: application
        version: 2.1.13
        repository: https://stakater.github.io/stakater-charts
      type: application
      version: 1.0.0
    ```

2. The values.yaml contains all the application specific **kubenetes resources** (deployments, configmaps, namespaces, secrets, services, route, podautoscalers, RBAC) for the particular environment. Configure Helm values as per application needs.

    Here is a minimal values file defined for an application with deployment,route,service.

    ```yaml
    # Name of the dependency chart
    application:
      # application name should be short so limit of 63 characters in route can be fulfilled.
      # Default route name formed is <application-name>-<namespace>.<base-domain> .
      # Config Maps have <application> prefixed

      # Replace applicationName with App Name
      applicationName: stakater-nordmart-review-ui

      deployment:
        # nexus-docker-config-forked is deployed in all tenant namespaces for pulling images
        imagePullSecrets: nexus-docker-config-forked
        image:
          repository: <nexus-docker-reg-url>/<tenant-name>/stakater-nordmart-review-ui
          tag: 1.0.0
      route:
        enabled: true
        port:
          targetPort: http
    ```

3. Make sure to validate the helm chart before doing a commit to the repository.
If your application contains dependency charts run the following command in deploy/ folder to download helm dependencies using **helm dependency build**.

    ```sh
    # Download helm dependencies in Chart.yaml
    cd deploy/
    # command info : helm dependency --help 
    helm dependency build
    ```

    ![helm-dependency-build](./images/helm-dependency-build.png)

4. Run the following command to see the Kubernetes manifests are being generated successfully and validate whether they match your required configuration. This simple helm chart generates deployment, service and route resources.

    > View Application Chart Usage [here](https://github.com/stakater/application)

    ```sh
    # Generates the chart against values file provided
    # and write the output to application-output.yaml
    # command info : helm template --help 
    helm template . > application-output.yaml
    ```

    Open the file to view raw Kubernetes manifests separated by '---' that ll be deployed for your application.

References to Explore:

- [`stakater-nordmart-review`](https://github.com/stakater-lab/stakater-nordmart-review/deploy)
- [`stakater-nordmart-review-ui`](https://github.com/stakater-lab/stakater-nordmart-review-ui/deploy)
- [All configurations available via Application Chart Values](https://github.com/stakater/application/blob/master/application/values.yaml)

## 4. Push Helm Chart to Nexus

After successfully pushing the image to Nexus. We need to package our helm chart and push to Nexus Helm Repo.
Run the following command to package the helm chart into compressed file.

```sh
# helm package [CHART_PATH]
helm package .
# output : successfully packaged chart and saved it to: /Desktop/Tasks/stakater/stakater-nordmart-review-ui/deploy/stakater-nordmart-review-ui-1.0.0.tgz
```

This command packages a chart into a versioned chart archive file.

```sh
# heml
curl -u "<helm_user>":"<helm_password>" <nexus-helm-reg-url> --upload-file "stakater-nordmart-review-ui-1.0.0.tgz"
```

## 5. Add application chart to `apps-gitops-config`

Navigate to `apps-gitops-config` repository and add a helm chart in path `gabbar/stakater-nordmart-review/dev` i.e. `<tenant-name>/<app-name>/dev`.

![app-in-dev-env](./images/app-in-dev-env.png)

```yaml
# <tenant-name>/<app-name>/dev/Chart.yaml
apiVersion: v2
name: <application-name>
description: A Helm chart for Kubernetes
dependencies:
  - name: <chart-name-in-deploy-folder>
    version: "1.0.0"
    repository: <nexus-helm-reg-url>/repository/helm-charts/
version: 1.0.0
-----------------------------------------
# <tenant-name>/<app-name>/dev/values.yaml
<dependency-name>:
  application:
    deployment:
      image:
        repository: <nexus-docker-reg-url>/stakater-nordmart-review-ui
        tag: 1.0.0
```

## 6. View Application in Cluster

Login into ArgoCD UI using Forecastle console. Visit the application against dev environment inside your tenant. Usual naming convention is **tenantName-envName-appName**. Make sure that there aren't any error while deploying during ArgoCD.

![dev-ArgoCD-app](./images/dev-argocd-app.png)

Visit the OpenShift console to verify the application deployment.

![review-web-pod](./images/review-web-pod.png)

![review-web-route](./images/review-web-route.png)

Visit the application url using routes to check if application is working as expected.

![review-web-ui](./images/review-web-ui.png)
