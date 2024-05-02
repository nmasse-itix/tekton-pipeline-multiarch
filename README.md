# Multi-architecture Tekton Pipeline

## Tekton configuration

```sh
oc patch configmap/feature-flags -n openshift-pipelines --type=merge -p '{"data":{"disable-affinity-assistant":"true"}}'
```

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
  uid: "0"
  gid: "0"
```

- [Create and configure access to EFS volumes in AWS](https://docs.openshift.com/container-platform/4.15/storage/container_storage_interface/persistent-storage-csi-aws-efs.html#efs-create-volume_persistent-storage-csi-aws-efs)

## Authentication to the registry

```sh
oc create secret docker-registry quay-authentication --docker-email=nmasse@redhat.com --docker-username=nmasse --docker-password=REDACTED --docker-server=quay.io
oc annotate secret/quay-authentication tekton.dev/docker-0=https://quay.io
```

## Pipeline manifests

```sh
oc apply -k tekton/
for yaml in examples/*/tekton/pipeline.yaml; do oc apply -f $yaml; done
```

## Run it!

```sh
for yaml in examples/*/tekton/pipelinerun.yaml; do oc create -f $yaml; done
```
