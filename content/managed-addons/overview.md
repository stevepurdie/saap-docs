# Overview

Here is the list of fully managed addons available on Stakater App Agility Platform:

Managed AddOn | Description
--- | ---
Logging | [ElasticSearch](https://www.elastic.co/), [Fluentd](https://www.fluentd.org/), [Kibana](https://www.elastic.co/kibana/)
Monitoring | [Grafana](https://github.com/integr8ly/grafana-operator), [Prometheus](https://github.com/coreos/prometheus-operator), [Thanos](https://thanos.io/)
CI (continuous integration) | [Tekton](https://tekton.dev/)
CD (continuous delivery) | [ArgoCD](https://argoproj.github.io/argo-cd/)
Internal alerting | [Alertmanager](https://prometheus.io/docs/alerting/latest/alertmanager/)
Service mesh | [Istio](https://istio.io/), [Kiali](https://kiali.io/), [Jaeger](https://www.jaegertracing.io/) (only one fully managed control plane)
Image scanning | [Trivy](https://github.com/aquasecurity/trivy)
Backups & Recovery | [Velero](https://velero.io/)
Authentication an SSO (for managed addons) | [Keycloak](https://access.redhat.com/documentation/en-us/red_hat_single_sign-on/7.6), [OAuth Proxy](https://github.com/oauth2-proxy/oauth2-proxy)
Secrets management | [Vault](https://www.vaultproject.io/)
Artifacts management (Docker, Helm and Package registry) | [Nexus](https://www.sonatype.com/products/repository-oss-download)
Code inspection | [SonarQube](https://www.sonarqube.org/)
Authorization & Policy Enforcement | [Open Policy Agent](https://www.openpolicyagent.org/) and [Gatekeeper](https://github.com/open-policy-agent/gatekeeper)
Log alerting | [Stakater Konfigurator](https://github.com/stakater/Konfigurator)
External (downtime) alerting | [Stakater IMC](https://github.com/stakater/IngressMonitorController), [UptimeRobot](https://uptimerobot.com/) (free tier)
Automatic application reload | [Stakater Reloader](https://github.com/stakater/Reloader)
Developer dashboard - Launchpad to discover applications | [Stakater Forecastle](https://github.com/stakater/Forecastle)
Multi-tenancy | [Stakater Multi Tenant Operator](https://docs.stakater.com/mto/index.html)
Feature environments, Preview Environments, Environments-as-a-Service | [Stakater Tronador](https://docs.stakater.com/tronador/#)
Clone secrets, configmaps, etc. | Stakater Replicator
GitOps Application Manager | Stakater Fabrikate
Management and issuance of TLS certificates | [cert-manager](https://github.com/jetstack/cert-manager)
Automated base image management | [Renovate](https://github.com/renovatebot/renovate)
Advanced cluster security | [StackRox](https://www.redhat.com/en/technologies/cloud-computing/openshift/advanced-cluster-security-kubernetes)
Intrusion detection | Falco (coming soon)
