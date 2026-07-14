# b-k8s

b cluster preset repository for the ScaleX Repo POC.

This repo is a mobilex-style cluster values repo. It does not carry the full app
catalog; it is consumed with the SmartX-style base repo:

```text
eecs-k8s   # base/framework/app catalog
b-k8s     # concrete child-cluster values and app patches
```

## Active feature set

Cilium/CoreDNS has been verified, so this child cluster now enables Ceph-backed
CSI as the next storage bootstrap layer:

```yaml
features:
  - org.ulagbulag.io/cni
  - org.ulagbulag.io/cni/lb-ipam
  - org.ulagbulag.io/csi
  - org.ulagbulag.io/distributed-storage-cluster/ceph
  - org.ulagbulag.io/distributed-storage-cluster/ceph/provisioning
  - org.ulagbulag.io/object-store/ceph
```

The POC nodes are labeled `ControlPlane`/`Compute`, so
`patches/rook-ceph-cluster/values.yaml` allows OSD placement on those roles.

For this POC, `rookCeph.useReplicas: false` is set because the available data disks are not present on three independent OSD failure domains yet.

The Cilium patch declares the `10.33.142.0/24` LoadBalancer pool. The Ceph
provisioning patch applies the one-replica post-config. The RGW patch owns the
shared B endpoint used by the federation POC.

`CephObjectStore/scalex-poc`, `StorageClass/ceph-bucket`, and the fixed RGW
LoadBalancer endpoint are B Infra. Application bucket claims are intentionally
not declared here: each feature Helm chart declares its namespaced OBC, and
`scalex-federation` selects the member cluster through Karmada policy.

## Child API endpoint

```text
https://10.33.201.190:6443
```
