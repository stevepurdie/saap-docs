# Artifact Management

[Nexus](https://www.sonatype.com/products/repository-pro) is a repository manager that can store and manage components, build artifacts, and release candidates in one central location. It can host multiple type of repositories like docker, Helm, Maven and more.

## Customer nexus endpoints and their purpose

| URL | Name | Purpose |
|---|---|---|
| `https://nexus-openshift-stakater-nexus.apps.<...>.kubeapp.cloud` | Nexus base URL for web view. | A dashboard where you can view all the repositories and settings. |
| `https://nexus-docker-openshift-stakater-nexus.<...>.kubeapp.cloud` | Nexus docker repository endpoint. | It points toward the docker repository in nexus, used to pull and push images. |
| `https://nexus-docker-proxy-openshift-stakater-nexus.<...>.kubeapp.cloud` | Nexus docker repository proxy. | This is a proxy URL that points towards Docker Hub, used to pull images from Docker Hub. |
| `https://nexus-helm-openshift-stakater-nexus.<...>.kubeapp.cloud/repository/helm-charts/` | Nexus Helm repository. | This is the nexus Helm repository endpoint, used to pull and push Helm charts. |
| `https://nexus-repository-openshift-stakater-nexus.<...>.kubeapp.cloud/repository/` | Nexus Maven repository. | This is the nexus Maven repository endpoint, used for Maven apps. |

We also support whitelisting for these endpoints. Please contact support if you want to enable whitelisting for specific IPs.
