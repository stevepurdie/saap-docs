# Understanding Kubernetes storage basics

Before you get started with provisioning storage, it is important to understand the Kubernetes concepts for storage, dynamic provisioning, static provisioning, as well as the storage classes that are available to you.

## Persistent volumes and persistent volume claims

Before you get started with provisioning storage, it is important to understand the Kubernetes concepts of a persistent volume and a persistent volume claim and how they work together in a cluster.

The following image shows the storage components in a cluster.

![Storage components in a cluster](images/cs_storage_pvc_pv.svg)

### Cluster

By default, every cluster is set up with a plug-in to provision file storage. You can choose to install other add-ons, such as the one for block storage. To use storage in a cluster, you must create a persistent volume claim, a persistent volume and a physical storage instance. When you delete the cluster, you have the option to delete related storage instances.

### App

To read from and write to your storage instance, you must mount the persistent volume claim (PVC) to your app. Different storage types have different read-write rules. For example, you can mount multiple pods to the same PVC for file storage. Block storage comes with a RWO (ReadWriteOnce) access mode so that you can mount the storage to one pod only.

### Persistent volume claim (PVC)

A PVC is the request to provision persistent storage with a specific type and configuration. To specify the persistent storage flavor that you want, you use Kubernetes storage classes. The cluster admin can define storage classes, or you can choose from one of the predefined storage classes in SAAP. When you create a PVC, the request is sent to the storage provider. Depending on the configuration that is defined in the storage class, the physical storage device is ordered and provisioned into your cloud infrastructure account. If the requested configuration does not exist, the storage is not created.

### Persistent volume (PV)

A PV is a virtual storage instance that is added as a volume to the cluster. The PV points to a physical storage device in your cloud infrastructure account and abstracts the API that is used to communicate with the storage device. To mount a PV to an app, you must have a matching PVC. Mounted PVs appear as a folder inside the container's file system.

### Physical storage

A physical storage instance that you can use to persist your data. Examples of physical storage in cloud include File Storage, Block Storage, Object Storage, and local worker node storage that you can use as SDS storage with Portworx. Clouds usually provide high availability for physical storage instances. However, data that is stored on a physical storage instance is not backed up automatically. Depending on the type of storage that you use, different methods exist to set up backup and restore solutions.

## Dynamic provisioning

## Static provisioning

## Storage classes
