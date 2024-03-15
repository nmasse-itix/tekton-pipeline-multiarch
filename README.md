# Multi-architecture Tekton Pipeline

## Setup on AWS

- [Install the AWS EFS CSI Driver Operator](https://docs.openshift.com/container-platform/4.15/storage/container_storage_interface/persistent-storage-csi-aws-efs.html#persistent-storage-csi-olm-operator-install_persistent-storage-csi-aws-efs)

- Install the AWS EFS CSI Driver

```yaml
apiVersion: operator.openshift.io/v1
kind: ClusterCSIDriver
metadata:
    name: efs.csi.aws.com
spec:
  managementState: Managed
```

- [Create an EFS volume](https://docs.aws.amazon.com/efs/latest/ug/gs-step-two-create-efs-resources.html)

- Create the StorageClass

```yaml
kind: StorageClass
apiVersion: storage.k8s.io/v1
metadata:
  name: efs-csi
provisioner: efs.csi.aws.com
parameters:
  provisioningMode: efs-ap
  fileSystemId: fs-123456
  directoryPerms: "700"
  basePath: "/pv"
```
