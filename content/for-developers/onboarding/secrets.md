# Adding Secrets

Now, we will setup applications to use consume secrets from Vault, using ExternalSecrets.

Multi-Tenant Operator [MTO] creates a path for each tenant in the vault. 
Each user in the cluster is part of a tenant. 
Users have access to the path corresponding to their tenant.
In this path, a key/value pair can be stored, and/or another path containing key/value pair can exist. 

Login to Vault to view your tenant path.

- Access Vault from  [Forecastle](https://forecastle-stakater-forecastle.apps.devtest.vxdqgl7u.kubeapp.cloud) console, search `Vault` and open the `Vault` tile.

    ![Forecastle-Vault](./images/forecastle-vault.png)
- From the drop-down menu under `Method`, select `OIDC` and click on `Sign in with OIDC Provider` and select `workshop` identity Provider

    ![Vault-ocic-login](./images/vault-ocic-login.png)

- You will be brought to the `Vault` console. You should see the key/value path for your tenant.
    

- External Secrets Operator is used to fetch secret data from vault, and create kubernetes secret in the cluster.
- External Secrets Operator uses SecretStore to make a connection to the vault.
- SecretStore uses ServiceAccount with vault label to access Vault.
- SecretStore and ServiceAccount is created in each tenant namespace.
- Each ExternalSecret CR contains reference to SecretStore to be used.

Stakater Application Chart contains support for ExternalSecret. 

```
externalSecret:
  enabled: true

  #SecretStore defines which SecretStore to use when fetching the secret data
  secretStore:
    name: example-secret-store
    kind: SecretStore # or ClusterSecretStore  

  # RefreshInterval is the amount of time before the values reading again from the SecretStore provider
  refreshInterval: "1m"
  files:
    secret-1-name:
      #Data defines the connection between the Kubernetes Secret keys and the Provider data 
      data:
        example-secret-key:
          remoteRef:
            key: example-provider-key
            property: example-provider-key-property

    secret-2-name:
      #Used to fetch all properties from the Provider key
      dataFrom:
        key: example-provider-key
      type: Opaque
      annotations:
        key: value
      labels:
        key: value
```
For more information on ExternalSecrets, see [External Secrets documentation](https://external-secrets.io/v0.8.1/introduction/overview/)