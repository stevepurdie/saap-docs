# Resource requirements for SAAP installation

> Glossary:
>
> **User workloads:** User applications (e-commerce frontend, backend APIs, etc.)
>
> **SAAP workloads:** Supporting applications for software lifecycle

## Summary

Resource requirements for a single SAAP cluster is as follows:

| Resource | Minimum | Recommended |
|:---|---:|---:|
| vCPUs (m) | 36 | 44 |
| Memory (Gib)  | 208 | 240 |
| Storage Block (Gib)| 900 | 1100 |
| Storage Snapshots (Gib) | 330 | 330 |
| Storage Buckets | 1 | 1 |
| Load Balancers* | 3 | 3 |
| Public IPs      | 2 | 2 |

!!! note
    * Load Balancers are only required for AWS, Azure and GCP.

### Minimum

The overall minimum resource requirements are:

| Machine pool role | Minimum size (vCPU x Memory x Storage) | Minimum pool size | vCPU | Total Memory (GiB) | Total Storage (GiB)
|:---|:---|---:|---:|---:|---:|
| Master | 6 x 24 x 120 | 3 | 18 | 72 | 360 |
| Infra | 4 x 16 x 120 | 2 | 8 | 32 | 240 |
| Monitoring | 4 x 32 x 120 | 1 | 4 | 32 | 120 |
| Worker | 4 x 16 x 120 | 3 | 12 | 48 | 360 |
| **Total** | | **9** | **42** | **184** | **1080** |

### Recommended

The recommended resource requirements are:

| Machine pool role | Minimum size (vCPU x Memory x Storage) | Minimum pool size | vCPU | Total Memory (GiB) | Total Storage (GiB) |
|:---|:---|---:|---:|---:|---:|
| Master | 6 x 24 x 120 | 3 | 18 | 72 | 360 |
| Infra | 4 x 16 x 120 | 2 | 8 | 32 | 240 |
| Monitoring | 4 x 32 x 120 | 1 | 4 | 32 | 120 |
| Logging | 4 x 16 x 120 | 1 | 4 | 16 | 120 |
| Pipeline | 4 x 16 x 120 | 1 | 4 | 16 | 120 |
| Worker | 4 x 16 x 120 | 3 | 12 | 48 | 360 |
| **Total** | | **11** | **50** | **216** | **1320** |

## Compute

### 3 x Master

The control plane, which is composed of master nodes, also known as the control plane, manages the SAAP cluster. The control plane nodes run the control plane. No user workloads run on master nodes.

### 2 x Infra

At least two infrastructure nodes are required for the SAAP infrastructure workloads:

| SAAP component | vCPU requirement (m) | Memory requirement (GiB) |
|---|---:|---:|
| [Stakater Forecastle](https://github.com/stakater/Forecastle)  | 50 | 0.20 |
| [Stakater Ingress Monitor Controller](https://github.com/stakater/IngressMonitorController)  | 150 | 0.60 |
| Stakater Kubehealth (SAAP components monitoring) | 150 | 0.40 |
| [Stakater Multi Tenant Operator](https://docs.stakater.com/mto/index.html)  | 600 | 1.20 |
| [Stakater Konfigurator](https://github.com/stakater/Konfigurator) | 20 | 0.30 |
| [Stakater Reloader](https://github.com/stakater/Reloader) | 20 | 0.50 |
| [Stakater Tronador](https://docs.stakater.com/tronador/#)  | 100 | 0.20 |
| [cert-manager](https://github.com/cert-manager/cert-manager)  | 100 | 1.50 |
| [External Secrets operator](https://github.com/external-secrets/external-secrets) | 50 | 0.30 |
| [Kubernetes replicator](https://github.com/mittwald/kubernetes-replicator) | 50 | 0.30 |
| [group-sync-operator](https://github.com/redhat-cop/group-sync-operator)  | 50 | 0.10 |
| [Helm operator](https://github.com/fluxcd/helm-operator) | 500 | 0.80 |
| [Nexus](https://github.com/sonatype/nexus-public)  | 200 | 1.60 |
| [OpenShift GitOps](https://docs.openshift.com/container-platform/4.7/cicd/gitops/understanding-openshift-gitops.html)  | 530 | 0.50 |
| [OpenShift Image Registry](https://docs.openshift.com/container-platform/4.11/registry/index.html) | 50 | 0.40 |
| [OpenShift Router](https://docs.openshift.com/container-platform/4.11/networking/ingress-operator.html)  | 300 |  0.30 |
| [SonarQube](https://www.sonarqube.org/)  | 350 | 1.50 |
| [Vault](https://github.com/hashicorp/vault)  | 255 | 0.36 |
| [Velero](https://github.com/vmware-tanzu/velero)  | 500 | 0.15 |
| [Volume Expander Operator](https://github.com/redhat-cop/volume-expander-operator)  | 50 | 0.10 |
| **Total** | **4275** | **11.61** |

No user workloads run on infrastructure nodes.

### 1 x Monitoring

Monitoring components to monitor `SAAP workloads` and user workloads are deployed on monitoring nodes. The monitoring stack includes the Prometheus stack (Prometheus, Grafana and Alertmanager).

Minimum one monitoring node must be used for all production deployments. For high availability consider using two monitoring nodes.

| Type of monitoring | SAAP component | vCPU requirement (m) | Memory requirement (GiB) |
|---|:---|---:|---:|
| **Infrastructure** |   |  | |
| | [Alertmanager](https://github.com/prometheus/alertmanager)   | 500 | 1.00 |
| | [Grafana](https://github.com/grafana/grafana)   | 50 | 0.10 |
| | [Node exporter](https://github.com/prometheus/node_exporter)  | 50 | 0.50 |
| | [Prometheus](https://github.com/prometheus/prometheus)   | 2500 | 7.50 |
| | [Thanos](https://github.com/thanos-io/thanos)   | 50 | 0.20 |
| **User Workloads** |   |  | |
| | [Alertmanager](https://github.com/prometheus/alertmanager) | 20 | 0.25 |
| | [Grafana](https://github.com/grafana/grafana) | 20 | 0.10 |
| | [Prometheus](https://github.com/prometheus/prometheus) | 100 | 2.50 |
| **Total**|    | **3290** | **12.15** |

For more details of monitoring, please visit [Creating Application Alerts](../sre/monitoring/app-alerts.md).

No user workloads run on monitoring nodes.

### 1 x Logging (optional)

Logging components aggregate all logs and store them centrally. These components run on logging nodes. The logging stack includes the EFK stack (Elasticsearch, Fluentd and Kibana).

The logging pool is optional, if there is no need for it, it will not be deployed. Logging infrastructure is still highly recommended for troubleshooting purposes.

Minimum one logging node is required. For high availability consider using three logging nodes.

| SAAP component | vCPU requirement (m) | Memory requirement (GiB) |
|---|---:|---:|
| Collector | 200 | 2.0 |
| [Elasticsearch](https://github.com/elastic/elasticsearch) | 500 | 4.0 |
| [Fluentd](https://github.com/fluent/fluentd) | 20 | 0.6 |
| [Kibana](https://github.com/elastic/kibana)| 300 | 0.5 |
| **Total** | **1020** | **7.1** |

No user workloads run on logging nodes.

### 1 x Pipeline (optional)

Pipeline nodes hold pods running for Tekton based CI/CD pipelines.

The pipeline pool is optional, if there is no need for it, it will not be deployed.

Minimum requirements for pipeline infrastructure is:

| SAAP component | vCPU requirement (m) | Memory requirement (GiB) |
|---|---:|---:|
| OpenShift pipelines | 100 | 0.2 |

No user workloads run on pipelines nodes.

### 3 x Worker

In a SAAP cluster, users run their applications on worker nodes. By default, a SAAP subscription comes with three worker nodes.

## Storage

### Block Storage

SAAP uses high performance disks i.e. `SSDs` for storage requirements which includes:

- Boot Volumes (attached to nodes for OS)
- Persistent Volumes (Additionally attached volumes for application consumption)

Following are the storage requirements used as Persistent Volumes consumed by `SAAP workloads`:

| SAAP component | Volume Size (GiB) |
|---|---:|
| Elasticsearch Logging | 300  |
| Nexus | 100 |
| Prometheus - Infrastructure Monitoring | 100  |
| Prometheus - workload Monitoring| 100 |
| SonarQube | 15 |
| Vault | 10 |
| **Total** | **625** |

### Object Storage

`1 x Object storage bucket` is required for keeping Backups of Kubernetes Objects.

### Volume Snapshot Requirements

Volume Snapshots are backups of volumes for critical `SAAP workloads` that only include `Nexus` and `Vault`

By default backups are taken daily and are retained for 3 days. So at a given instance 3 day old backups for `SAAP workloads` are kept.

| SAAP component | PV size | backup frequency | Backup size (GiB) |
|---|---:|---:|---:|
| Nexus | 100 | 3 | 300 |
| Vault | 10 | 3 | 30 |
| **Total** | | | **330** |

## Network

### Load Balancers

#### For AWS, Azure, GCP

Each SAAP cluster deploys `3 x Loadbalancers`:

- 2 x Public (for cluster API and cluster dashboard)

- 1 x Private (for control plane communication)

#### For OpenStack

No LoadBalancers required.

### Floating IPs

#### For AWS, Azure, GCP

No additional Floating IPs/Public IPs are required.

#### For OpenStack

`2 x Floating IPs` are required (for cluster API and cluster dashboard).
