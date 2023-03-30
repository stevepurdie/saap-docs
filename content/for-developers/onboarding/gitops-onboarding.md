# GitOps Onboarding

Let us set up Stakater Opinionated GitOps Structure.

Stakater's GitOps structure is composed of two repositories; one for deploying infrastructural resources, and one for deploying the application workloads.

For the purpose of this tutorial, we will be using the name `nordmart-infra-gitops-config` for the former repository and `nordmart-apps-gitops-config` for the latter.
You can name these two repositories anything you want but make sure the names are descriptive.

Let's set these two repositories up!!

## Nordmart Infra GitOps Config

The cluster scoped infrastructural configurations are deployed through this repository.

To make things easier, we have created a [template](https://github.com/stakater/infra-gitops-config.git) that you can use to create your infra repository.

1. Open up your SCM and create any empty repository
1. Now let's copy the structure that we saw in the [template](https://github.com/stakater/infra-gitops-config.git). Add a folder bearing your cluster's name at the root of the repository that you just created. If you plan on using this repository for multiple clusters, add a folder for each cluster.
1. Inside the folder created in step 2, add two folders; one named `argocd-apps`, and another one named `tenant-operator-config`
    > The `argocd-apps` folder will contain ArgoCD applications that will _watch_ different resources added to the same repository. Let's spare ourselves from the details for now.
1. Open the tenant-operator-config folder and add two folder inside it: quotas, tenants
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
      sandboxConfig:
        enabled: true
        private: true
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

1. Now that the quota and the tenant have been added, let's create an ArgoCD application in the argpcd-apps folder that will point to these resources and help us deploy them.
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
        repoURL: 'REPLACE ME WITH URL OF THE REPOSITORY THAT YOU ARE CURRENTLY WORKING ON'
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

## `nordmart-apps-gitops-config`

Single source of truth for declarative workloads to be deployed on cluster.
