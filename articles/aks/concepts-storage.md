---
title: Concepts - Storage in Azure Kubernetes Services (AKS)
description: Learn about storage in Azure Kubernetes Service (AKS), including volumes, persistent volumes, storage classes, and claims
services: container-service
ms.topic: conceptual
ms.date: 03/30/2022

---

# Storage options for applications in Azure Kubernetes Service (AKS)

Applications running in Azure Kubernetes Service (AKS) may need to store and retrieve data. While some application workloads can use local, fast storage on unneeded, emptied nodes, others require storage that persists on more regular data volumes within the Azure platform. 

Multiple pods may need to:
* Share the same data volumes. 
* Reattach data volumes if the pod is rescheduled on a different node. 

Finally, you may need to inject sensitive data or application configuration information into pods.

This article introduces the core concepts that provide storage to your applications in AKS:

- [Volumes](#volumes)
- [Persistent volumes](#persistent-volumes)
- [Storage classes](#storage-classes)
- [Persistent volume claims](#persistent-volume-claims)

![Storage options for applications in an Azure Kubernetes Services (AKS) cluster](media/concepts-storage/aks-storage-options.png)

## Volumes

Kubernetes typically treats individual pods as ephemeral, disposable resources. Applications have different approaches available to them for using and persisting data. A *volume* represents a way to store, retrieve, and persist data across pods and through the application lifecycle.

Traditional volumes are created as Kubernetes resources backed by Azure Storage. You can manually create data volumes to be assigned to pods directly, or have Kubernetes automatically create them. Data volumes can use: [Azure Disks][disks-types], [Azure Files][storage-files-planning], [Azure NetApp Files][azure-netapp-files-service-levels], or [Azure Blobs][storage-account-overview]. 

### Azure Disks

Use *Azure Disks* to create a Kubernetes *DataDisk* resource. Disks types include: 
* Ultra Disks
* Premium SSDs
* Standard SSDs
* Standard HDDs

> [!TIP]
>For most production and development workloads, use Premium SSD. 

Since Azure Disks are mounted as *ReadWriteOnce*, they're only available to a single pod. For storage volumes that can be accessed by multiple pods simultaneously, use Azure Files.

### Azure Files
Use *Azure Files* to mount an SMB 3.1.1 share or NFS 4.1 share backed by an Azure storage accounts to pods. Files let you share data across multiple nodes and pods and can use:
* Azure Premium storage backed by high-performance SSDs
* Azure Standard storage backed by regular HDDs

### Azure NetApp Files
* Ultra Storage 
* Premium Storage
* Standard Storage 

### Azure Blob Storage
* Block Blobs 

### Volume types
Kubernetes volumes represent more than just a traditional disk for storing and retrieving information. Kubernetes volumes can also be used as a way to inject data into a pod for use by the containers. 

Common volume types in Kubernetes include:

#### emptyDir

Commonly used as temporary space for a pod. All containers within a pod can access the data on the volume. Data written to this volume type persists only for the lifespan of the pod. Once you delete the pod, the volume is deleted. This volume typically uses the underlying local node disk storage, though it can also exist only in the node's memory.

#### secret

You can use *secret* volumes to inject sensitive data into pods, such as passwords. 
1. Create a Secret using the Kubernetes API. 
1. Define your pod or deployment and request a specific Secret. 
    * Secrets are only provided to nodes with a scheduled pod that requires them.
    * The Secret is stored in *tmpfs*, not written to disk. 
1. When you delete the last pod on a node requiring a Secret, the Secret is deleted from the node's tmpfs. 
   * Secrets are stored within a given namespace and can only be accessed by pods within the same namespace.

#### configMap

You can use *configMap* to inject key-value pair properties into pods, such as application configuration information. Define application configuration information as a Kubernetes resource, easily updated and applied to new instances of pods as they're deployed. 

Like using a Secret:
1. Create a ConfigMap using the Kubernetes API. 
1. Request the ConfigMap when you define a pod or deployment. 
   * ConfigMaps are stored within a given namespace and can only be accessed by pods within the same namespace.

## Persistent volumes

Volumes defined and created as part of the pod lifecycle only exist until you delete the pod. Pods often expect their storage to remain if a pod is rescheduled on a different host during a maintenance event, especially in StatefulSets. A *persistent volume* (PV) is a storage resource created and managed by the Kubernetes API that can exist beyond the lifetime of an individual pod.

You can use Azure Disks or Files to provide the PersistentVolume. As noted in the [Volumes](#volumes) section, the choice of Disks or Files is often determined by the need for concurrent access to the data or the performance tier.

![Persistent volumes in an Azure Kubernetes Services (AKS) cluster](media/concepts-storage/persistent-volumes.png)

A PersistentVolume can be *statically* created by a cluster administrator, or *dynamically* created by the Kubernetes API server. If a pod is scheduled and requests currently unavailable storage, Kubernetes can create the underlying Azure Disk or Files storage and attach it to the pod. Dynamic provisioning uses a *StorageClass* to identify what type of Azure storage needs to be created.

## Storage classes

To define different tiers of storage, such as Premium and Standard, you can create a *StorageClass*. 

The StorageClass also defines the *reclaimPolicy*. When you delete the pod and the persistent volume is no longer required, the reclaimPolicy controls the behavior of the underlying Azure storage resource. The underlying storage resource can either be deleted or kept for use with a future pod.

For clusters using the [Container Storage Interface (CSI) drivers][csi-storage-drivers] the following extra `StorageClasses` are created:

| Permission | Reason |
|---|---|
| `managed-csi` | Uses Azure StandardSSD locally redundant storage (LRS) to create a Managed Disk. The reclaim policy ensures that the underlying Azure Disk is deleted when the persistent volume that used it is deleted. The storage class also configures the persistent volumes to be expandable, you just need to edit the persistent volume claim with the new size. |
| `managed-csi-premium` | Uses Azure Premium locally redundant storage (LRS) to create a Managed Disk. The reclaim policy again ensures that the underlying Azure Disk is deleted when the persistent volume that used it is deleted. Similarly, this storage class allows for persistent volumes to be expanded. |
| `azurefile-csi` | Uses Azure Standard storage to create an Azure File Share. The reclaim policy ensures that the underlying Azure File Share is deleted when the persistent volume that used it is deleted. |
| `azurefile-csi-premium` | Uses Azure Premium storage to create an Azure File Share. The reclaim policy ensures that the underlying Azure File Share is deleted when the persistent volume that used it is deleted.|

Unless you specify a StorageClass for a persistent volume, the default StorageClass will be used. Ensure volumes use the appropriate storage you need when requesting persistent volumes. 

> [!IMPORTANT]
> Starting in Kubernetes version 1.21, AKS will use CSI drivers only and by default.  The `default` class will be the same as `managed-csi`

You can create a StorageClass for additional needs using `kubectl`. The following example uses Premium Managed Disks and specifies that the underlying Azure Disk should be *retained* when you delete the pod:

```yaml
apiVersion: storage.k8s.io/v1
kind: StorageClass
metadata:
  name: managed-premium-retain
provisioner: disk.csi.azure.com
parameters:
  skuName: Premium_LRS
reclaimPolicy: Retain
volumeBindingMode: WaitForFirstConsumer
allowVolumeExpansion: true
```

> [!NOTE]
> AKS reconciles the default storage classes and will overwrite any changes you make to those storage classes.

## Persistent volume claims

A PersistentVolumeClaim requests storage of a particular StorageClass, access mode, and size. The Kubernetes API server can dynamically provision the underlying Azure storage resource if no existing resource can fulfill the claim based on the defined StorageClass. 

The pod definition includes the volume mount once the volume has been connected to the pod.

![Persistent volume claims in an Azure Kubernetes Services (AKS) cluster](media/concepts-storage/persistent-volume-claims.png)

Once an available storage resource has been assigned to the pod requesting storage, PersistentVolume is *bound* to a PersistentVolumeClaim. Persistent volumes are 1:1 mapped to claims.

The following example YAML manifest shows a persistent volume claim that uses the *managed-premium* StorageClass and requests a Disk *5Gi* in size:

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: azure-managed-disk
spec:
  accessModes:
  - ReadWriteOnce
  storageClassName: managed-premium-retain
  resources:
    requests:
      storage: 5Gi
```

When you create a pod definition, you also specify:
* The persistent volume claim to request the desired storage. 
* The *volumeMount* for your applications to read and write data. 

The following example YAML manifest shows how the previous persistent volume claim can be used to mount a volume at */mnt/azure*:

```yaml
kind: Pod
apiVersion: v1
metadata:
  name: nginx
spec:
  containers:
    - name: myfrontend
      image: mcr.microsoft.com/oss/nginx/nginx:1.15.5-alpine
      volumeMounts:
      - mountPath: "/mnt/azure"
        name: volume
  volumes:
    - name: volume
      persistentVolumeClaim:
        claimName: azure-managed-disk
```

For mounting a volume in a Windows container, specify the drive letter and path. For example:

```yaml
...      
       volumeMounts:
        - mountPath: "d:"
          name: volume
        - mountPath: "c:\k"
          name: k-dir
...
```

## Next steps

For associated best practices, see [Best practices for storage and backups in AKS][operator-best-practices-storage].

To see how to use CSI drivers, see the following how-to articles:

- [Enable Container Storage Interface(CSI) drivers for Azure disks and Azure Files on Azure Kubernetes Service(AKS)][csi-storage-drivers]
- [Use Azure disk Container Storage Interface(CSI) drivers in Azure Kubernetes Service(AKS)][azure-disk-csi]
- [Use Azure Files Container Storage Interface(CSI) drivers in Azure Kubernetes Service(AKS)][azure-files-csi]
- [Integrate Azure NetApp Files with Azure Kubernetes Service][azure-netapp-files] 

For more information on core Kubernetes and AKS concepts, see the following articles:

- [Kubernetes / AKS clusters and workloads][aks-concepts-clusters-workloads]
- [Kubernetes / AKS identity][aks-concepts-identity]
- [Kubernetes / AKS security][aks-concepts-security]
- [Kubernetes / AKS virtual networks][aks-concepts-network]
- [Kubernetes / AKS scale][aks-concepts-scale]

<!-- EXTERNAL LINKS -->

<!-- INTERNAL LINKS -->
[disks-types]: ../virtual-machines/disks-types.md
[storage-files-planning]: ../storage/files/storage-files-planning.md
[azure-netapp-files-service-levels]: ../azure-netapp-files/azure-netapp-files-service-levels.md
[storage-account-overview]: ../storage/common/storage-account-overview.md
[csi-storage-drivers]: csi-storage-drivers.md
[azure-disk-csi]: azure-disk-csi.md
[azure-netapp-files]: azure-netapp-files.md
[azure-files-csi]: azure-files-csi.md
[aks-concepts-clusters-workloads]: concepts-clusters-workloads.md
[aks-concepts-identity]: concepts-identity.md
[aks-concepts-scale]: concepts-scale.md
[aks-concepts-security]: concepts-security.md
[aks-concepts-network]: concepts-network.md
[operator-best-practices-storage]: operator-best-practices-storage.md
[csi-storage-drivers]: csi-storage-drivers.md
