# Restore PVC data with GitOps

Restore PVC data when its managed in GitOps by ArgoCD.

## 1. Prerequisite

Setup `velero-cli` [doc](./velero-cli.md)

## 2. Take backup

To take backup with Velero you have two options:

### 2.1: Option # 1 - Via CLI

```sh
velero backup create <NAME-OF-BACKUP> --include-namespaces <APPLICATION-NAMESPACE> --namespace openshift-velero
```

### 2.2: Option # 2 - Via CR

```yaml
apiVersion: velero.io/v1
kind: Backup
metadata:
  name: <NAME-OF-BACKUP>
  namespace: openshift-velero
spec:
  includedNamespaces:
  - <APPLICATION-NAMESPACE>
  includedResources:
  - '*'
  storageLocation: default
  ttl: 2h0m0s
```

## 3. Disable self heal in ArgoCD

Disable self heal in ArgoCD application that is managing PVC so it does not recreate resources from GitOps.

```yaml
  syncPolicy:
    automated:
      prune: true
      selfHeal: false
```

## 4. Delete PVC

Scale down `statefulset` pod so PVC can be deleted

```sh
oc scale statefulsets <NAME> --replicas 0
```

Delete the PVC which you want to restore data so that its created again by Velero.

```sh
oc delete pvc <PVC-NAME> -n <NAMESPACE> 
```

## 5. Restore Velero Backup

To restore backup with Velero you have two options:

### 5.1: Option # 1 - Via CLI

```sh
velero restore create --from-backup <NAME-OF-BACKUP> --namespace openshift-velero
```

### 5.2: Option # 2 - Via CR

```yaml
apiVersion: velero.io/v1
kind: Restore
metadata:
  name: <NAME-OF-BACKUP>
  namespace: openshift-velero
spec:
  backupName: <NAME-OF-BACKUP>
  excludedResources:
    - nodes
    - events
    - events.events.k8s.io
    - backups.velero.io
    - restores.velero.io
    - resticrepositories.velero.io
  includedNamespaces:
    - '*'
```

After a successful restore, you should be able to see pod up and running with restored backup data

## 6. Scale up `statefulset` again

Scale up `statefulset` set so new pod can be attached to restored PVC

```sh
oc scale statefulsets <NAME> --replicas 0
```

## 7. Validate

Validate the data exists in the database.

## 8. Enable self heal again

Enable self heal so ArgoCD start managing resources again.

```yaml
  syncPolicy:
    automated:
      prune: true
      selfHeal: true

```
