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

1. Open up your SCM and create any empty repository.

> Follow along GitHub/GitLab documentation for configuring other organization specific requirements set for source code repositories.

1. Create a secret with read permissions over this repository. Navigate to following section for more info [Configure Repository Secret for ArgoCD](gitops-argocd-secrets.md). Provide this secret to stakater-admin for it to be deployed with your ArgoCD instance.
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

1. Deploy an ArgoCD application on the cluster pointing to `<cluster-name>/argocd-apps` directory. You will need to ask Stakater Admin to create it as part of ArgoCD Instance.

1. Login to ArgoCD and check if `infra-gitops-config` application is present. Validate the child application `tenant-operator-config`.

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

### Create the repository

1. Open up your SCM and create any empty repository named `apps-gitops-config`.

> Follow along GitHub/GitLab documentation for configuring other organization specific requirements set for source code repositories.

1. Create a secret with read permissions over this repository. Navigate to following section for more info [Configure Repository Secret for ArgoCD](gitops-argocd-secrets.md). We'll use this secret later in [Linking Apps GitOps with Infra GitOps
](#linking-apps-gitops-with-infra-gitops).

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

1. We need to create ArgoCD applications that will deploy the apps of apps structure defined in our `apps-gitops-config` repository.

1. Suppose we want to deploy our application workloads of our dev (CLUSTER_NAME) cluster. We can create an ArgoCD application for `apps-gitops-config` repository pointing to `argocd-apps/dev (argocd-apps/CLUSTER_NAME)` inside `cluster/argocd-apps/` folder in `infra-gitops-config` repository.

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

1. We need to add this resource inside `argocd-apps` folder in `dev/argocd-apps (CLUSTER_NAME/argocd-apps)`.

   ```bash
   ├── dev
       └── argocd-apps
           └── apps-gitops-config.yaml
   ```

1. Now lets add the secret required by ArgoCD for reading `apps-gitops-config` repository. Lets add a folder called `argocd-secrets` at `cluster/`. This will contain secrets required by ArgoCD for authentication over repositories.

   ```bash
   ├── dev
       ├── argocd-apps
       |   └── apps-gitops-config.yaml
       └── argocd-secrets
   ```

1. Add a secret in Vault at `root-tenant/<repo-name>` path depending upon whether you configure SSH or Token Access. Add a external secret custom resource in `cluster/argocd-secrets/<repo-name>.yaml` folder. Use the following template :

   ```yaml
   # Name: apps-gitops-config-external-secret.yaml (<repo-name>-external-secret.yaml)
   # Path: dev/argocd-secrets/
   apiVersion: external-secrets.io/v1beta1
   kind: ExternalSecret
   metadata:
     name: <repo-name>
     namespace: argocd-ns
   spec:
     secretStoreRef:
       name: root-tenant-secret-store
       kind: SecretStore
     refreshInterval: "1m"
     target:
       name: <repo-name>
       creationPolicy: 'Owner'
     dataFrom:
       - key: <repo-name>
   ```

1. Add an ArgoCD application pointing to this directory `dev/argocd-secrets/` inside `dev/argocd-apps/argocd-secrets.yaml`.

   ```yaml
   # Name: argocd-secrets.yaml (FOLDER_NAME.yaml)
   # Path: dev/argocd-apps/
   apiVersion: argoproj.io/v1alpha1
   kind: Application
   metadata:
     name: argocd-secrets
     namespace: openshift-gitops
   spec:
     destination:
       namespace: openshift-gitops
       server: 'https://kubernetes.default.svc'
     project: root-tenant
     source:
       path: dev/argocd-secrets/
       repoURL: 'INFRA_GITOPS_REPO_URL'
       targetRevision: HEAD
     syncPolicy:
       automated:
         prune: true
         selfHeal: true
   ```

   ```bash
   ├── dev
       ├── argocd-apps
       |   ├── argocd-secrets.yaml
       |   └── apps-gitops-config.yaml
       └── argocd-secrets
           └── apps-gitops-config-external-secret.yaml
   ```

1. Login to ArgoCD and check if the secret is deployed by opening `argocd-secrets` application in `infra-gitops-config` application.

## View Apps-of-Apps structure on ArgoCD

1. Login to ArgoCD and view `apps-gitops-config` application and explore the `apps-of-apps` structure.
