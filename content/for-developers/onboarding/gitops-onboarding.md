# GitOps Onboarding

Let us set up Stakater Opinionated GitOps Structure.

Stakater's GitOps structure is composed of two repositories; one for deploying infrastructural resources, and one for deploying the application workloads.

For the purpose of this tutorial, we will be using the name `infra-gitops-config` for the former repository and `apps-gitops-config` for the latter.
You can name these two repositories anything you want but make sure the names are descriptive.

Let's set these two repositories up!!

## Infra GitOps Config

The cluster scoped infrastructural configurations are deployed through this repository.

To make things easier, we have created a [template](https://github.com/stakater/infra-gitops-config.git) that you can use to create your infra repository.

1. Open up your SCM and create any empty repository
1. Now let's copy the structure that we saw in the [template](https://github.com/stakater/infra-gitops-config.git). Add a folder bearing your cluster's name at the root of the repository that you just created. 
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
      - prod
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

1. Now that the quota and the tenant have been added, let's create an ArgoCD application in the argocd-apps folder that will point to these resources and help us deploy them.
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
      project: default
      syncPolicy:
        automated:
          prune: true
          selfHeal: true
    ```

Make sure you replace the `repoUrl` and all instances of `CLUSTER_NAME` with your cluster's name.
If you notice the path, you will realize that this application is pointing to 'tenant-operator-config' folder housing your tenant and quotas.

We have set up the basic structure for our infra repository. Let's move on to the apps repository.

More Info on **Tenant** and **Qouta** at : [Multi Tenant Operator Custom Resources](https://docs.stakater.com/mto/main/customresources.html)
## `Apps Gitops Config`

This repository is the single source of truth for declarative workloads to be deployed on cluster. It separates workloads per tenant.

To make things easier, we have created a [template](https://github.com/stakater-lab/apps-gitops-config.git) that you can use to create your infra repository.
### Hierarchy

Tenant (Product) owns Applications which are promoted to different Environments on different clusters.

Cluster >> Tenants (teams/products) >> Applications >> Environments (distributed among Clusters)

A cluster can hold multiple tenants; and each tenant can hold multiple applications; and each application be deployed into multiple environments.

This gitops structure supports:

- Multiple clusters
- Multiple tenants/teams/products
- Multiple apps
- Multiple environments (both static and dynamic)

### Add a tenant
Lets proceed by adding a tenant to the apps gitops repository.

1. Create a folder at root level for your tenant. Lets use **gabbar** as tenant name which was deployed in the previous section via infra gitops config.

      ```bash
      ├── gabbar
      ```

    Inside this folder we can define multiple applications per tenant. 

1. We need to create a **argocd-apps** folder inside this **gabbar** folder. This folder will deploy the applications defined inside its siblings folders (in gabbar folder).
      ```bash
      ├── gabbar
          └── argocd-apps
      ```

1. Lets add an application for tenant gabbar, Lets call this application **stakater-nordmart-review**. Create a folder named stakater-nordmart-review in gabbar folder.

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

    We need the corresponding folders inside argocd-apps folder and define argocd apps pointing to these folders.

      ```bash
      ├── gabbar
          ├── argocd-apps
             ├── dev
             │   └── stakater-nordmart-review-dev.yaml
             └── stage
                 └── stakater-nordmart-review-stage.yaml
      ```

    Create an argocd application inside dev folder that points to dev directory in stakater-nordmart-review. Create a file named APP_NAME-ENV.yaml with following spec:
  
        apiVersion: argoproj.io/v1alpha1
        kind: Application
        metadata:
          name: TENANT_NAME-ENV_NAME-APP_NAME
          namespace: openshift-gitops
        spec:
          destination:
            namespace: TARGET_NAMESPACE
            server: 'https://kubernetes.default.svc'
          project: TENANT_NAME
          source:
            path: TENANT_NAME/APP_NAME/ENV_NAME
            repoURL: 'APPS_GITOPS_REPO_URL'
            targetRevision: HEAD
          syncPolicy:
            automated:
              prune: true
              selfHeal: true

      Replace *TENANT_NAME* with **gabbar**, *ENV_NAME* with **dev**, *APP_NAME* with **stakater-nordmart-review**, *TARGET_NAMESPACE* with gabbar-dev.

      Similarly create another argocd application inside stage folder that points to dev directory in stakater-nordmart-review.

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
1. Now we need to add argocd apps for environments defined in gabbar/argocd-apps to the relevant cluster folders. Create dev and stage folders inside argocd-apps folder. 
      ```bash
      ├── argocd-apps
          ├── dev
          └── stage
      ```
    > Folders in argocd-apps corresponds to clusters, these folder contain argocd apps pointing to 1 or more environments inside multiple tenant folders, Folders in gabbar/argocd-apps correspond to environments.

    Next, create the following argocd apps:

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


## Linking Apps Gitops with Infra Gitops
We need to create argocd applications that will deploy the apps of apps structure defined in our apps-gitops-config.

Suppose we want to deploy our application workloads of our dev cluster. We can create an argocd apps for apps-gitops-repo pointing to argocd-apps/CLUSTER_NAME (argocd-apps/dev). Following template should be used:

```
apiVersion: argoproj.io/v1alpha1
kind: Application
metadata:
  name: apps-gitops-repo
  namespace: openshift-gitops
spec:
  destination:
    namespace: openshift-gitops
    server: 'https://kubernetes.default.svc'
  project: default
  source:
    path: argocd-apps/dev
    repoURL: 'https://github.com/your-org/apps-gitops-repo.git'
    targetRevision: HEAD
  syncPolicy:
    automated:
      prune: true
      selfHeal: true
```
