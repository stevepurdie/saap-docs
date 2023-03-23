# Security

## Authentication provider

Authentication for the cluster is configured as part of cluster creation process. SAAP is not an identity provider, and all access to the cluster must be managed by the customer as part of their integrated solution. Provisioning multiple identity providers provisioned at the same time is supported. The following identity providers are supported:

- GitHub or GitHub Enterprise OAuth
- GitLab OAuth
- Google OAuth
- LDAP
- OpenID connect

## Privileged containers

Privileged containers are not available by default on SAAP. The `anyuid` and `nonroot` Security Context Constraints (SCC) are available for members of the `sca` group, and should address many use cases. Privileged containers are only available for cluster-admin users.

## Customer administrator user

In addition to normal users

## Cluster administration role

As an administrator
