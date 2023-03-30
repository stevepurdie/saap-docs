# Routes

Following routes are available:

|   Username   | Privileges                               |                              URL                                                    |
| ------------ | ------------------------------------------- |------------------------------------------------------------------------------------ |
| `helm-user`    | `nx-repository-view-helm-*-*`       |  __`http://nexus.openshift-stakater-nexus.svc.cluster.local:8081/repository/helm-charts/`__  |
| `docker-user`  | `nx-repository-view-docker-*-*` |  __`https://nexus-docker-openshift-stakater-nexus.{CLUSTER_DOMAIN}/`__  |
| `mnn-rw-user`  | `nx-repository-view-{maven2,NuGet,NPM}-*-*` |  __`https://nexus-repository-openshift-stakater-nexus.{CLUSTER_DOMAIN}/repository/maven-public/`__  |
| `mnn-ro-user`  | `nx-repository-view-{maven2,NuGet,NPM}-*-{browse,read}` |  __`https://nexus-repository-openshift-stakater-nexus.{CLUSTER_DOMAIN}/`__  |
