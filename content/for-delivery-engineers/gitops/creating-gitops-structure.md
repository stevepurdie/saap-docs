# GitOps Onboarding

Let us set up Stakater Opinionated GitOps Structure.

Stakater's GitOps structure is composed of two repositories; one for deploying infrastructural resources, and one for deploying the application workloads.

For the purpose of this tutorial, we will be using the name `infra-gitops-config` for the former repository and `apps-gitops-config` for the latter.
You can name these two repositories anything you want but make sure the names are descriptive.

Let's set these two repositories up!!

## Infra GitOps Config

The cluster scoped infrastructural configurations are deployed through this repository.

To make things easier, we have created a [template](https://github.com/stakater/infra-gitops-config.git) that you can use to create your infra repository.

Team Stakater will create a root [Tenant](https://docs.stakater.com/mto/main/customresources.html#2-tenant), which will then create a root AppProject.
This AppProject will be used to sync all the Applications in `Infra Gitops Config` and it will provide visibility of these Applications in ArgoCD UI to customer cluster admins.

1. Open up your SCM and create any empty repository
1. Now let's copy the structure that we saw in the [template](https://github.com/stakater/infra-gitops-config.git). Add a folder bearing your cluster's name say `dev` at the root of the repository that you just created.
    > If you plan on using this repository for multiple clusters, add a folder for each cluster.
1. Inside the folder created in step 2, add two folders; one named `argocd-apps`, and another one named `tenant-operator-config`
    > The `argocd-apps` folder will contain ArgoCD applications that will _watch_ different resources added to the same repository. Let's spare ourselves from the details for now.
1. Open the `tenant-operator-config` folder and add two folders inside it: `quotas` and `tenants`
1. The tenants folder will contain the tenant you want to add to your cluster. Let's create one called `gabbar` by adding the file below:

    ```yaml
    apiVersion: tenantoperator.stakater.com/v1beta1
    kind: Tenant
    metadata:
      name: gabbar
    spec:
      quota: gabbar-large
      owners:
        users:
        - abc@gmail.com
      argocd:
          sourceRepos:
          - 'https://github.com/your-organization/infra-gitops-config'
          - 'https://github.com/your-organization/apps-gitops-config'
      templateInstances:
      - spec:
          template: tenant-vault-access
          sync: true
      namespaces:
      - build
      - dev
      - stage
      specificMetadata:
      - namespaces:
          - gabbar-build
        annotations:
          openshift.io/node-selector: node-role.kubernetes.io/pipeline=
    ```

1. We also need to add a quota for our `gabbar` tenant in our `quotas` folder created in step 4. So let's do it using the file below. The name of this quota need to match the name you specified in tenant CR.

    ```yaml
    apiVersion: tenantoperator.stakater.com/v1beta1
    kind: Quota
    metadata:
      name: gabbar-large
      annotations:
        quota.tenantoperator.stakater.com/is-default: "false"
    spec:
      resourcequota:
        hard:
          requests.cpu: "16"
          requests.memory: 32Gi
      limitrange:
        limits:
          - defaultRequest:
              cpu: 10m
              memory: 50Mi
            type: Container
    ```

1. Now that the quota and the tenant have been added, let's create an ArgoCD application in the `argocd-apps` folder that will point to these resources and help us deploy them.
Open up the `argocd-apps` folder and add the following file to it:

    ```yaml
    apiVersion: argoproj.io/v1alpha1
    kind: Application
    metadata:
      name: CLUSTER_NAME-tenant-operator-config
      namespace: openshift-gitops
    spec:
      destination:
        namespace: openshift-gitops
        server: 'https://kubernetes.default.svc'
      source:
        path: CLUSTER_NAME/tenant-operator-config
        repoURL: 'INFRA_GITOPS_REPO_URL'
        targetRevision: HEAD
        directory:
          recurse: true
      project: root-tenant
      syncPolicy:
        automated:
          prune: true
          selfHeal: true
    ```

    Make sure you replace the `repoUrl` and all instances of `CLUSTER_NAME` with your cluster's name.
    If you notice the path, you will realize that this application is pointing to 'tenant-operator-config' folder housing your tenant and quotas.

1. Deploy an ArgoCD application on the cluster pointing to `<cluster-name>/argocd-apps` directory. You will need to ask Stakater Admin to create it as part of Openshift GitOps Instance.

We have set up the basic structure for our infra repository. Let's move on to the apps repository.

More Info on **Tenant** and **Quota** at : [Multi Tenant Operator Custom Resources](https://docs.stakater.com/mto/main/customresources.html)

## Apps GitOps Config

This repository is the single source of truth for declarative workloads to be deployed on cluster. It separates workloads per tenant.

To make things easier, we have created a [template](https://github.com/stakater-lab/apps-gitops-config.git) that you can use to create your infra repository.

### Hierarchy

Tenant (Product) owns Applications which are promoted to different Environments on different clusters.

Cluster >> Tenants (teams/products) >> Applications >> Environments (distributed among Clusters)

A cluster can hold multiple tenants; and each tenant can hold multiple applications; and each application be deployed into multiple environments.

This GitOps structure supports:

- Multiple clusters
- Multiple tenants/teams/products
- Multiple apps
- Multiple environments (both static and dynamic)

### Add a tenant

Lets proceed by adding a tenant to the `apps-gitops-config` repository.

1. Create a folder at root level for your tenant. Lets use `gabbar` as tenant name which was deployed in the previous section via `infra-gitops-config` repository.

      ```bash
      ├── gabbar
      ```

    Inside this folder we can define multiple applications per tenant.

1. We need to create a `argocd-apps` folder inside this `gabbar` folder. This folder will deploy the applications defined inside its siblings folders (in `gabbar` folder).

      ```bash
      ├── gabbar
          └── argocd-apps
      ```

1. Lets add an application for tenant `gabbar`, Lets call this application `stakater-nordmart-review`. Create a folder named `stakater-nordmart-review` in `gabbar` folder.

      ```bash
      ├── gabbar
          └── stakater-nordmart-review
      ```

    This application has two environments: dev and stage. Former is deployed to dev cluster and latter is deployed to stage cluster.
    We need to create two new folders now:

      ```bash
      ├── gabbar
          └── stakater-nordmart-review
              ├── dev
              └── stage
      ```

    We need the corresponding folders inside `argocd-apps` folder and define ArgoCD applications pointing to these folders.

      ```bash
      ├── gabbar
          ├── argocd-apps
             ├── dev
             │   └── stakater-nordmart-review-dev.yaml
             └── stage
                 └── stakater-nordmart-review-stage.yaml
      ```

    Create an ArgoCD application inside dev folder that points to dev directory in `stakater-nordmart-review`. Create a file named `APP_NAME.yaml` with following spec:

      ```yaml
      # Name: stakater-nordmart-review.yaml(APP_NAME.yaml)
      # Path: gabbar/argocd-apps/dev (TENANT_NAME/argocd-apps/ENV_NAME/)
      apiVersion: argoproj.io/v1alpha1
      kind: Application
      metadata:
        name: gabbar-dev-stakater-nordmart-review
        namespace: openshift-gitops
      spec:
        destination:
          namespace: TARGET_NAMESPACE
          server: 'https://kubernetes.default.svc'
        project: gabbar
        source:
          path: gabbar/stakater-nordmart-review/dev
          repoURL: 'APPS_GITOPS_REPO_URL'
          targetRevision: HEAD
        syncPolicy:
          automated:
            prune: true
            selfHeal: true
      ```

    Similarly create another ArgoCD application inside stage folder that points to stage directory in `stakater-nordmart-review`.

      ```yaml
      # Name: stakater-nordmart-review.yaml (APP_NAME.yaml)
      # Path: gabbar/argocd-apps/stage (TENANT_NAME/argocd-apps/ENV_NAME/)
      apiVersion: argoproj.io/v1alpha1
      kind: Application
      metadata:
        name: gabbar-stage-stakater-nordmart-review
        namespace: openshift-gitops
      spec:
        destination:
          namespace: gabbar-stage
          server: 'https://kubernetes.default.svc'
        project: gabbar
        source:
          path: gabbar/stakater-nordmart-review/stage
          repoURL: 'APPS_GITOPS_REPO_URL'
          targetRevision: HEAD
        syncPolicy:
          automated:
            prune: true
            selfHeal: true
      ```

      > Find the template file [here](https://github.com/stakater-lab/apps-gitops-config/blob/main/01-TENANT_NAME/00-argocd-apps/APP_NAME-ENV_NAME.yamlSample)

      After performing all the steps you should have the following folder structure:

      ```bash
      ├── gabbar
          ├── argocd-apps
             ├── dev
             │   └── stakater-nordmart-review-dev.yaml
             └── stage
                 └── stakater-nordmart-review-stage.yaml
          └── stakater-nordmart-review
              ├── dev
              └── stage
      ```

1. Add ArgoCD applications for these environments (dev & stage) defined in `gabbar/argocd-apps`. These environment belong to specific cluster.

    Create dev and stage folders at `argocd-apps/` to represent dev and stage cluster.

      ```bash
      ├── argocd-apps
          ├── dev
          └── stage
      ```

    > Folders in `argocd-apps` corresponds to clusters, these folder contain ArgoCD applications pointing to 1 or more environments inside multiple tenant folders per cluster, Folders in `gabbar/argocd-apps` correspond to environments.

    Next, create the following ArgoCD applications:

      ```yaml
      # Name: gabbar-stage.yaml (TENANT_NAME-ENV_NAME.yaml)
      # Path: argocd-apps/stage
      apiVersion: argoproj.io/v1alpha1
      kind: Application
      metadata:
        name: gabbar-stage
        namespace: openshift-gitops
      spec:
        destination:
          namespace: gabbar-stage
          server: 'https://kubernetes.default.svc'
        project: gabbar
        source:
          path: gabbar/argocd-apps/stage
          repoURL: 'APPS_GITOPS_REPO_URL'
          targetRevision: HEAD
        syncPolicy:
          automated:
            prune: true
            selfHeal: true
      ---
      # Name: gabbar-dev.yaml (TENANT_NAME-ENV_NAME.yaml)
      # Path: argocd-apps/dev
      apiVersion: argoproj.io/v1alpha1
      kind: Application
      metadata:
        name: gabbar-dev
        namespace: openshift-gitops
      spec:
        destination:
          namespace: gabbar-dev
          server: 'https://kubernetes.default.svc'
        project: gabbar
        source:
          path: gabbar/argocd-apps/dev
          repoURL: 'APPS_GITOPS_REPO_URL'
          targetRevision: HEAD
        syncPolicy:
          automated:
            prune: true
            selfHeal: true
      ```

      > Find the template file [here](https://github.com/stakater-lab/apps-gitops-config/blob/main/00-argocd-apps/CLUSTER_NAME/TENANT_NAME-ENV_NAME.yamlSample)

## Linking Apps GitOps with Infra GitOps

> You will need to do this once per `apps-gitops-config` repository.

We need to create ArgoCD applications that will deploy the apps of apps structure defined in our `apps-gitops-config` repository.

Suppose we want to deploy our application workloads of our dev (CLUSTER_NAME) cluster. We can create an ArgoCD application for `apps-gitops-config` repository pointing to `argocd-apps/dev (argocd-apps/CLUSTER_NAME)`.

```yaml
apiVersion: argoproj.io/v1alpha1
kind: Application
metadata:
  name: apps-gitops-repo
  namespace: openshift-gitops
spec:
  destination:
    namespace: openshift-gitops
    server: 'https://kubernetes.default.svc'
  project: root-tenant
  source:
    path: argocd-apps/dev
    repoURL: 'APPS_GITOPS_REPO_URL'
    targetRevision: HEAD
  syncPolicy:
    automated:
      prune: true
      selfHeal: true
```

> Find the template file [here](https://github.com/stakater/infra-gitops-config/blob/main/CLUSTER_NAME/argocd-apps/apps-gitops-config.yamlSample)

We need to add this resource inside `argocd-apps` folder in `infra-gitops-config/dev (infra-gitops-config/CLUSTER_NAME)`.

  ```bash
  ├── dev
      └── argocd-apps
          └── apps-gitops-config.yaml
  ```

## Configure Repository Secret for ArgoCD

We need to add secret in ArgoCD namespace that will allow read access over the `apps-gitops-config` repository created in previous section.

### Configure token or SSH keys

You need to configure token or SSH based access over the `apps-gitops-config` repository.
Use the following links:

- For token access
    - [`Create a personal access token`](https://docs.github.com/en/enterprise-server@3.4/authentication/keeping-your-account-and-data-secure/creating-a-personal-access-token) or [`Create a fine-grained token`](https://github.blog/2022-10-18-introducing-fine-grained-personal-access-tokens-for-github/)
- For SSH Access
    - [`Generate SSH Key Pair`](https://docs.github.com/en/authentication/connecting-to-github-with-ssh/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent#generating-a-new-ssh-key)
    - [`Add SSH Public key to your GitHub Account`](https://docs.github.com/en/authentication/connecting-to-github-with-ssh/adding-a-new-ssh-key-to-your-github-account) or [`Add Deploy Key to your Repository`](https://docs.github.com/en/authentication/connecting-to-github-with-ssh/managing-deploy-keys#deploy-keys)

### Create a Secret with Token or SSH key

Create a Kubernetes Secret in ArgoCD namespace with repository credentials. Each repository secret must have a url field and, depending on whether you connect using HTTPS, SSH, username and password (for HTTPS), sshPrivateKey (for SSH).

Example for HTTPS:

```yaml
apiVersion: v1
kind: Secret
metadata:
  name: private-repo
  namespace: argocd-ns
  labels:
    argocd.argoproj.io/secret-type: repository
stringData:
  type: git
  url: https://github.com/argoproj/private-repo
  # Use a personal access token in place of a password.
  password: my-pat-or-fgt
  username: my-username
```

Example for SSH:

```yaml
apiVersion: v1
kind: Secret
metadata:
  name: private-repo
  namespace: argocd
  labels:
    argocd.argoproj.io/secret-type: repository
stringData:
  type: git
  url: git@github.com:argoproj/my-private-repository
  sshPrivateKey: |
    -----BEGIN OPENSSH PRIVATE KEY-----
    ...
    -----END OPENSSH PRIVATE KEY-----
```

> More Info on Connecting ArgoCD to Private Repositories [here](https://argo-cd.readthedocs.io/en/stable/operator-manual/declarative-setup/#repositories)

Login to the ArgoCD UI. Click `Setting` from left sidebar, then `Repositories` to view connected repositories.

  ![`ArgoCD-repositories`](images/ArgoCD-repositories.png)

> Make sure connection status is successful

### Possible Issues

If connection status is failed, hover over the ❌ adjacent to `Failed` to view the error.

#### SSH Handshake Failed: Key mismatch

> Related GitHub Issue: [here](https://github.com/argoproj/argo-cd/issues/7723)

If you see the following error. Check `argocd-ssh-known-hosts-cm` config map in ArgoCD namespace to verify that public key for repository server is added as `ssh_known_hosts`.
![`ArgoCD-repo-connection-ssh-issue`](images/ArgoCD-repo-connection-ssh-issue.png)

Some known hosts public keys might be missing in `argocd-ssh-known-hosts-cm` for older ArgoCD versions, Find full list of public keys against repository server [here](https://argo-cd.readthedocs.io/en/stable/operator-manual/declarative-setup/#ssh-known-host-public-keys).
