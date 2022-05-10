# aws-efs-csi-driver operator

An operator to deploy the [AWS EFS CSI driver](https://github.com/openshift/aws-efs-csi-driver) in OKD.

This operator is installed by OLM.

# Quick start

To build and run the operator locally:

```shell
# Create only the resources the operator needs to run via CLI
oc apply -f - <<EOF
apiVersion: operator.openshift.io/v1
kind: ClusterCSIDriver
metadata:
    name: efs.csi.aws.com
spec:
  logLevel: Normal
  managementState: Managed
  operatorLogLevel: Trace
EOF

# Build the operator
make

# Set the environment variables
export DRIVER_IMAGE=gcr.io/k8s-staging-cloud-provider-ibm/ibm-powervs-block-csi-driver:v0.1.0-alpha.3
export PROVISIONER_IMAGE=quay.io/openshift/origin-csi-external-provisioner:latest
export ATTACHER_IMAGE=quay.io/openshift/origin-csi-external-attacher:latest
export RESIZER_IMAGE=quay.io/openshift/origin-csi-external-resizer:latest
export NODE_DRIVER_REGISTRAR_IMAGE=quay.io/openshift/origin-csi-node-driver-registrar:latest
export LIVENESS_PROBE_IMAGE=quay.io/openshift/origin-csi-livenessprobe:latest
export KUBE_RBAC_PROXY_IMAGE=quay.io/openshift/origin-kube-rbac-proxy:latest

# Run the operator via CLI
./ibm-powervs-block-csi-driver-operator start --kubeconfig $KUBECONFIG --namespace openshift-cluster-csi-drivers
```

# OLM

To build an bundle + index images, use `hack/create-bundle`.

```shell
cd hack
./create-bundle registry.ci.openshift.org/ocp/4.9:aws-efs-csi-driver registry.ci.openshift.org/ocp/4.9:aws-efs-csi-driver-operator quay.io/<my-repo>/efs-bundle quay.io/<my-repo>/efs-index
```

At the end it will print a command that creates `Subscription` for the newly created index image.

TODO: update the example to use `quay.io/openshift` once the images are mirrored there. `registry.ci.openshift.org` is not public.