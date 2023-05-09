# Cluster Update Process

Cluster update is shared responsibility of Stakater SRE and Customer.

## Prerequisites

- Ensure all Operators previously installed through Operator Lifecycle Manager (OLM) are updated to their latest version in their latest channel. Updating the Operators ensures they have a valid update path when the default OperatorHub catalogs switch from the current minor version to the next during a cluster update.

- Review the list of APIs that were removed in previous Kubernetes version, migrate any affected components to use the new API version. See [Navigating Kubernetes API deprecations and removals](https://access.redhat.com/articles/6955985) for more information on evaluating API usage and migrating away from these APIs.

Stakater requires prior approval to upgrade the cluster, you may experience some downtime during the upgrade process as each node is upgraded. To ensure the stability of the cluster and the proper level of support, Stakater only switch from a stable channel to a fast channel, and vice versa.
Only upgrading to a newer version is supported. Reverting or rolling back your cluster to a previous version is not supported.
