# Konfigurator

A kubernetes operator that can dynamically generate app configuration when kubernetes resources change

## Features

- Render Configurations to
  - ConfigMap
  - Secret
- Support for GO Templating Engine
- Custom helper functions
- Support to watch the following Kubernetes Resources
  - Pods
  - Services
  - Ingresses

## Deploying to Kubernetes

Deploying Konfigurator requires:

1. Deploying CRD to your cluster
2. Deploying Konfigurator operator

You can deploy CRDs either together or separately with the operator in the helm chart by setting `deployCRD` in values.yaml file.

```bash
helm repo add stakater https://stakater.github.io/stakater-charts

helm repo update

helm install stakater/konfigurator
```

Once Konfigurator is running, you can start creating resources supported by it. For details about its custom resources, look [here](https://github.com/stakater/Konfigurator/tree/master/docs/konfigurator-template.md).

To make Konfigurator work globally, you would have to change the `WATCH_NAMESPACE` environment variable to "" in values.yaml. e.g. change `WATCH_NAMESPACE` section to:

```yaml
  env:
  - name: WATCH_NAMESPACE
    value: ""
```