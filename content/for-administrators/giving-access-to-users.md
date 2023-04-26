# User Access

By default, users logged in (via OAuth external IDPs) do not have any permissions

Two types of permissions can be granted to a user:

- [SAAP Cluster Admin](#saap-cluster-admin)
- [Tenant Level Permissions](#tenant-level-permissions)

## SAAP Cluster Admin

SAAP Cluster is an administrator level role for a user (with restrictive access). A user with this role can:

- Create/Manage/Delete Tenants
- Read cluster status (Overview page)
- Administrate non-managed Projects/Namespaces
- Install/Modify/Delete operators in non-managed Projects/Namespaces

To grant this permission to a user please open a support case with Username/Email of the desired user.

## Tenant level Permissions

These permissions are granted per Tenant and are only restricted to the tenant's Namespaces/Projects. For detailed explanation of these roles see [Tenant Member Roles](https://docs.stakater.com/mto/main/tenant-roles.html)

These roles can be granted by [SAAP Cluster Admin](#saap-cluster-admin) by creating/editing the *Tenant* CR.

To grant Tenant level permissions see detailed example for [Tenant CR](https://docs.stakater.com/mto/main/customresources.html#2-tenant)
