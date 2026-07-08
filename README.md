# b-k8s

b cluster preset repository for the ScaleX Repo POC.

This repo is a mobilex-style cluster values repo. It does not carry the full app
catalog; it is consumed with the SmartX-style base repo:

```text
eecs-k8s   # base/framework/app catalog
b-k8s     # concrete child-cluster values and app patches
```

## Initial feature set

Only CNI is enabled for the first child-cluster bootstrap stage:

```yaml
features:
  - org.ulagbulag.io/cni
```

CSI/Ceph is intentionally left for a later stage after Cilium and CoreDNS are
verified healthy.

## Child API endpoint

```text
https://10.33.201.190:6443
```
