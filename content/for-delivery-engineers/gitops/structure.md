# GitOps structure

We manage GitOps with two different kinds of repository with different purpose enlisted below:

- **`Apps GitOps Config`**: Used for delivering applications belonging to tenants.
- **`Infra GitOps Config`**: Used for delivering cluster scoped resources for application tenants or other services.

## Structure of Apps GitOps Config

### Applications

Inside tenants folder, there is a separate folder of each application that belongs to a tenant. The name of the folder should match repository name in SCM.

### Environments

Inside each application folder, there is a separate folder of each environment where application will gets deployed to. Inside each environment folder there will be actual deployment files.

Deployment files can only be vanilla yaml files, Helm chart and Kustomize repository that are supported by ArgoCD.

### ArgoCD Applications (at tenant level)

Inside `argocd-apps` folder there is a folder for each environment. In each environment folder there are `argocd-apps` custom resource that watches deployments files in ```<tenant>/<app>/<env>```.

### ArgoCD Applications (at root level)

Inside `argocd-apps` folder, there are multiple clusters defined. Each cluster holds separate application environments for multiple tenants.

```sh
├── 01-tenant
│   ├── 01-app
│   │   ├── 00-env
│   │   │   └── deployment.yaml
│   │   ├── 01-env
│   │   │   ├── Chart.yaml
│   │   │   └── values.yaml
│   ├── 02-app
│   │   ├── 01-env
│   │   │   ├── Chart.yaml
│   │   │   └── values.yaml
│   │   ├── 02-env
│   │   │   ├── Chart.yaml
│   │   │   └── values.yaml
│   │   └── 03-env
│   │       ├── Chart.yaml
│   │       └── values.yaml
│   └── argocd-apps
│       ├── 00-env
│       │   └── 00-env-01-app.yaml (points to 01-app/00-env)
│       ├── 01-env
│       │   ├── 01-env-01-app.yaml (points to 01-app/01-env)
│       │   └── 01-env-02-app.yaml (points to 02-app/01-env)
│       ├── 02-env
│       │   └── 02-env-02-app.yaml (points to 02-app/02-env)
│       └── 03-env
│           ├── 03-env-01-app.yaml (points to 01-app/03-env)
│           └── 03-env-02-app.yaml (points to 02-app/03-env)
├── argocd-apps
│   ├── 01-cluster
│   │   ├── 01-tenant-00-env.yaml (points to 01-tenant/argocd-apps/00-env)
│   │   └── 01-tenant-01-env.yaml (points to 01-tenant/argocd-apps/01-env)
│   └── 02-cluster
│       ├── 01-tenant-02-env.yaml (points to 01-tenant/argocd-apps/02-env)
│       └── 01-tenant-03-env.yaml (points to 01-tenant/argocd-apps/03-env)
└── README.md
```

## Structure of Infra GitOps Config

In each cluster folder there are folders containing resource for particular cluster. These include resources that are cluster scoped or don't belong to application tenant. It is further divided into 2 kinds of folders:

### Cluster Deployments

They are logical grouping of resources belong to an operator, tenant or service into folders. Each folder includes resources that are cluster scoped or don't belong to application tenant e.g. `tenant-operator` folder contains custom resources of ```Multi Tenant Operator```. They are following

- quotas: Amount of resource (configmaps, cpus, memory, i.e.) for each tenant that can consume.
- tenants: Contains file for each team. It contain information of members that are part of tenant.

### ArgoCD Applications

This folder is a starting point of all configuration in the cluster. Inside the folder we have following ArgoCD applications that deploy resources in other sibling folders:

- **`tenant-operator.yaml`**: Responsible for creating tenants and quotas inside `tenant-operator` folder configuration in the cluster.
- **`apps-gitops-config.yaml`**: Points to corresponding cluster folder in `apps-gitops-config` repository. This deploys the `apps-of-apps` structure that deploys all applications environments for the cluster.

```sh
├── 01-cluster
│   ├── argocd-apps
|   │   ├── apps-gitops-config.yaml (points to argocd-apps/01-cluster/ of seprate apps-gitops-config repository)
|   │   ├── minio-operator.yaml (points to 01-cluster/minio-operator)
|   │   └── tenant-operator.yaml (points to 01-cluster/tenant-operator)
│   ├── minio-operator
│   │   ├── Chart.yaml
│   │   └── values.yaml
|   └── tenant-operator
|       ├── quotas
|       │   ├── 01-quota.yaml
|       │   ├── 02-quota.yaml
|       │   └── 03-quota.yaml
|       └── tenants
|           ├── 01-tenant.yaml
|           └── 02-tenant.yaml
├── 02-cluster
│   ├── argocd-apps
|   │   ├── apps-gitops-config.yaml (points to argocd-apps/02-cluster/ of seprate apps-gitops-config repository)
|   │   └── tenant-operator.yaml (points to 02-cluster/tenant-operator)
|   └── tenant-operator
|       ├── quotas
|       │   └── 04-quota.yaml
|       └── tenants
|           └── 04-tenant.yaml
└── README.md
```
