# Configure Repository Secret for ArgoCD

We need to add secret in ArgoCD namespace that will allow read access over the `apps-gitops-config` repository created in previous section.

## Configure token or SSH keys

You need to configure token or SSH based access over the `apps-gitops-config` repository.
Use the following links:

- For token access
    - [`Create a personal access token`](https://docs.github.com/en/enterprise-server@3.4/authentication/keeping-your-account-and-data-secure/creating-a-personal-access-token) or [`Create a fine-grained token`](https://github.blog/2022-10-18-introducing-fine-grained-personal-access-tokens-for-github/)
- For SSH Access
    - [`Generate SSH Key Pair`](https://docs.github.com/en/authentication/connecting-to-github-with-ssh/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent#generating-a-new-ssh-key)
    - [`Add SSH Public key to your GitHub Account`](https://docs.github.com/en/authentication/connecting-to-github-with-ssh/adding-a-new-ssh-key-to-your-github-account) or [`Add Deploy Key to your Repository`](https://docs.github.com/en/authentication/connecting-to-github-with-ssh/managing-deploy-keys#deploy-keys)

## Create a Secret with Token or SSH key

Create a Kubernetes Secret in ArgoCD namespace with repository credentials. Each repository secret must have a url field and, depending on whether you connect using HTTPS, SSH, username and password (for HTTPS), sshPrivateKey (for SSH).

Example for HTTPS:

```yaml
apiVersion: v1
kind: Secret
metadata:
  name: private-repo
  namespace: argocd-ns
  labels:
    argocd.argoproj.io/secret-type: repository
stringData:
  type: git
  url: https://github.com/argoproj/private-repo
  # Use a personal access token in place of a password.
  password: my-pat-or-fgt
  username: my-username
```

Example for SSH:

```yaml
apiVersion: v1
kind: Secret
metadata:
  name: private-repo
  namespace: argocd
  labels:
    argocd.argoproj.io/secret-type: repository
stringData:
  type: git
  url: git@github.com:argoproj/my-private-repository
  sshPrivateKey: |
    -----BEGIN OPENSSH PRIVATE KEY-----
    ...
    -----END OPENSSH PRIVATE KEY-----
```

> More Info on Connecting ArgoCD to Private Repositories [here](https://argo-cd.readthedocs.io/en/stable/operator-manual/declarative-setup/#repositories)

Login to the ArgoCD UI. Click `Setting` from left sidebar, then `Repositories` to view connected repositories.
> Make sure connection status is successful

  ![`ArgoCD-repositories`](images/ArgoCD-repositories.png)

> Ask stakater-admin or user belonging to `customer-root-tent` to add this secret via Vault and External Secrets to ArgoCD namespace.

## Possible Issues

If connection status is failed, hover over the ❌ adjacent to `Failed` to view the error.

### SSH Handshake Failed: Key mismatch

> Related GitHub Issue: [here](https://github.com/argoproj/argo-cd/issues/7723)

If you see the following error. Check `argocd-ssh-known-hosts-cm` config map in ArgoCD namespace to verify that public key for repository server is added as `ssh_known_hosts`.
![`ArgoCD-repo-connection-ssh-issue`](images/ArgoCD-repo-connection-ssh-issue.png)

Some known hosts public keys might be missing in `argocd-ssh-known-hosts-cm` for older ArgoCD versions, Find full list of public keys against repository server [here](https://argo-cd.readthedocs.io/en/stable/operator-manual/declarative-setup/#ssh-known-host-public-keys).
