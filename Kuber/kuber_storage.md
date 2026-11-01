# Kubernetes Storage and Persistence

## EmptyDir
Pod level storage. this volume is ephemeral. not use in production.

## Local
Local volumes is persistent and defined at Node level. not use in production.

## Persistent volume
A PersistentVolume (PV) is a piece of storage in the cluster that has been provisioned by an administrator or dynamically provisioned using Storage Classes. It is a resource in the cluster just like a node is a cluster resource. 

`kubectl get persistentvolume` or `kubectl get pv` 

### Static vs. dynamic provisioning:
| Feature                    | Static                                       | Dynamic                                   |
| :------------------------- | :------------------------------------------- | :---------------------------------------- |
| Who prepares the storage?  | Administrator                                | Provisioner, on demand                    |
| Who creates the PV object? | Administrator                                | Kubernetes provisioning workflow          |
| StorageClass required?     | Not necessarily                              | Yes, for the dynamic workflow             |
| Operational effort         | More manual work                             | Less manual work                          |
| Typical use                | Pre-existing disks, manually managed storage | Cloud disks, CSI-backed storage           |
| Flexibility                | Depends on prepared volumes                  | Easier to request storage per application |

## Persistent Volume Claim 
A PersistentVolumeClaim (PVC) is a request for storage by a user. It is similar to a Pod. Pods consume node resources and PVCs consume PV resources. Pods can request specific levels of resources (CPU and Memory). Claims can request specific size and access modes.

Kubernetes binds a PVC only to a PV with the same class name (and enough capacity, and a compatible access mode)

relation between pvc an pv is one-to-one. means if a pv bound to one pvc, it connot to bound to other.

```yaml
apiVersion: v1
kind: PersistentVolume
metadata:
  name: local-volume
spec:
  capacity:
    storage: 1Gi
  volumeMode: Filesystem
  accessModes:
    - ReadWriteOnce
  persistentVolumeReclaimPolicy: Retain
  storageClassName: local-storage
  local:
    path: /mnt/disks/local1         # already exist on that node. Kubernetes won’t create it
  # nodeAffinity forces the Scheduler to place any Pod using a PVC bound to this volume onto the specific node
  nodeAffinity:
    required:
      nodeSelectorTerms:
        - matchExpressions:
            - key: kubernetes.io/hostname
              operator: In
              values: ['minikube']  # only the node named "minikube"
---
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: local-volume-claim
spec:
  resources:
    requests:
      storage: 1Gi
  volumeMode: Filesystem
  accessModes:
    - ReadWriteOnce
  storageClassName: local-storage
---
apiVersion: v1
kind: Pod
metadata:
  name: local-vol-pod
  labels:
    name: local-vol-pod
spec:
  containers:
    - name: local-vol
      image: busybox:1.36.1
      command:
        - 'sh'
        - '-c'
        - 'sleep 3600'
      resources:
        limits:
          memory: '128Mi'
          cpu: '500m'
      volumeMounts:
        - name: local-volume
          mountPath: /mnt/local
  volumes:
    - name: local-volume
      persistentVolumeClaim:
        claimName: local-volume-claim
```

### access modes
**ReadWriteOnce**
    the volume can be mounted as read-write by a single node, and can be used by any number of pods whithin that node.
**ReadOnlyMany**
    the volume can be mounted as read-only by many nodes.
**ReadWriteMany**
    the volume can be mounted as read-write by many nodes.
**ReadWriteOncePod**
    the volume can be mounted as read-write by a single Pod across the whole cluster.

### Reclaim policies
What happens after you delete a PVC depends on the PV's reclaim policy.
- **Retain**: The PV and underlying storage are retained for manual recovery or cleanup.
- **Delete**: Kubernetes and the provisioner delete the PV and, where supported and configured, the underlying storage.    

### common states:
- **Available**: PV is available for a claim.
- **Bound**: PV and PVC are paired.
- **Released**: The claim was deleted, but the PV has not necessarily been made available again.
- **Pending**: PVC has not yet been bound; dynamic provisioning may be waiting or failing.

## Config map

## Secrets
