# Roles and Users

## Users

We divide the nexus users into 2 groups according to the interaction method:

1. human users and
2. machines users

### 1. Human Users

Human user interacts with nexus using UI. For Human users, we are using SSO authentication method. So once we grant proper roles to the users. You can refer the role mapping rules mentioned above as the list format. At the first login, account information is registered in the `rh-sso` which is coming from the IdP. If that information is enough, then registration is performed automatically. If some information is missing, user is requested to update account information and user has to fill out all information. (NOTE: Email is not filled automatically).

### SSO (OAuth) Roles

For human users which login via SSO we have following roles available.

|    Nexus Role       |    Privileges       |
| ------------------- | ------------------- |
| `nexus-oauth-admin`   |nx-all               |
| `nexus-oauth-viewer`  |nx-repository-view-*-*-browse, nx-repository-view-*-*-read |
| `nexus-oauth-editor`  |nx-datastores-all, nx-blobstores-all, nx-analytics-all, nx-repository-admin-*-*-* |

On first login you automatically get `nexus-oauth-viewer`. See [Granting Admin privilege to user for nexus on Keycloak](./grant-nexus-admin-keycloak.md) on how to configure admin role with keycloak for nexus.

### 2. Machine Users

Machine user interacts with nexus using API or CLI and we are using nexus local user authentication for machines users

Here is machine users list:

1. `helm-user`: is able to use with OpenShift service DNS (public link is not available)
1. `docker-user`: is able to use with the dedicated route for docker registry. Because the docker client does not allow a context as part of the path to a registry, a specific and separate port is used for docker registry. And also to use the docker registry at the node level (kubelet) the docker registry should be exposed. So we use a route which has the OpenShift cluster gateway IP in the whitelist.

`mnn-users` are able to access maven2, NuGet and NPM repositories (`mnn` stands for Maven, NuGet and NPM); developers should use these users if they want to connect their package manager with nexus:

1. `mnn-ro-user` user has read only permission on repositories.
1. `mnn-rw-user` user has read&write permission on repositories.

The following local users are created automatically in nexus so you can use it. The credentials are rotated according to the secret management policy automatically. And you can find the passwords for these users in Vault.
