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
  - org.ulagbulag.io/workload-namespace
```

The POC nodes are labeled `ControlPlane`/`Compute`, so
`patches/rook-ceph-cluster/values.yaml` allows OSD placement on those roles.

For this POC, `rookCeph.useReplicas: false` is set because the available data disks are not present on three independent OSD failure domains yet.

The Cilium patch declares the `10.33.142.0/24` LoadBalancer pool. The Ceph
provisioning patch applies the one-replica post-config. The RGW patch owns the
shared B endpoint used by the federation POC.

`CephObjectStore/scalex-poc`, `StorageClass/ceph-bucket`, the fixed RGW
LoadBalancer endpoint, and bucket claims are B Infra. This patch declares two
claims with separate consumers:

- `tower-harbor-registry` for Tower Harbor
- `scalex-rgw-analysis-web/rgw-analysis-web-bucket` for the Federation POC

The latter keeps the existing `rgw-analysis-web-poc` bucket identity and data,
but its lifecycle now belongs exclusively to `b-k8s`. Federation keeps only a
non-secret runtime binding that reads the Rook-generated Secret/ConfigMap and
normalizes them for the workload. Neither claim nor its credential values are
submitted to Karmada from Git.

`patches/workload-namespace/values.yaml` makes
`Namespace/scalex-rgw-analysis-web` an explicit B Infra prerequisite. Karmada's
source namespace opts out of automatic namespace propagation, so deleting a
Federation release cannot delete the namespace that contains the B-owned OBC.

A bucket must have exactly one declarative owner. Do not declare the same OBC
from Federation, a feature chart, or another cluster repo.

## Child API endpoint

```text
https://10.33.201.190:6443
```
