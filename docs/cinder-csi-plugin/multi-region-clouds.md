# MultiAz/Region Configuration

### Create region/cloud secrets

```
apiVersion: v1
stringData:
  cloud: region-one
kind: Secret
metadata:
  name: openstack-config-region-one
  namespace: kube-system
type: Opaque
---
apiVersion: v1
stringData:
  cloud: region-two
kind: Secret
metadata:
  name: openstack-config-region-two
  namespace: kube-system
type: Opaque
```

### Create storage Class for dedicated azs

```
allowVolumeExpansion: true
allowedTopologies:
- matchLabelExpressions:
  - key: topology.kubernetes.io/region
    values:
    - dc1-int
apiVersion: storage.k8s.io/v1
kind: StorageClass
metadata:
  annotations:
    storageclass.kubernetes.io/is-default-class: "true"
  name: sc-region-one
parameters:
  availability: nova
  csi.storage.k8s.io/controller-publish-secret-name: openstack-config-region-one
  csi.storage.k8s.io/controller-publish-secret-namespace: kube-system
  csi.storage.k8s.io/node-publish-secret-name: openstack-config-region-one
  csi.storage.k8s.io/node-publish-secret-namespace: kube-system
  csi.storage.k8s.io/node-stage-secret-name: openstack-config-region-one
  csi.storage.k8s.io/node-stage-secret-namespace: kube-system
  csi.storage.k8s.io/provisioner-secret-name: openstack-config-region-one
  csi.storage.k8s.io/provisioner-secret-namespace: kube-system
provisioner: cinder.csi.openstack.org
reclaimPolicy: Delete
volumeBindingMode: Immediate
---
allowVolumeExpansion: true
allowedTopologies:
- matchLabelExpressions:
  - key: topology.kubernetes.io/region
    values:
    - dc2-int
apiVersion: storage.k8s.io/v1
kind: StorageClass
metadata:
  name: sc-region-two
parameters:
  availability: nova
  csi.storage.k8s.io/controller-publish-secret-name: openstack-config-region-two
  csi.storage.k8s.io/controller-publish-secret-namespace: kube-system
  csi.storage.k8s.io/node-publish-secret-name: openstack-config-region-two
  csi.storage.k8s.io/node-publish-secret-namespace: kube-system
  csi.storage.k8s.io/node-stage-secret-name: openstack-config-region-two
  csi.storage.k8s.io/node-stage-secret-namespace: kube-system
  csi.storage.k8s.io/provisioner-secret-name: openstack-config-region-two
  csi.storage.k8s.io/provisioner-secret-namespace: kube-system
provisioner: cinder.csi.openstack.org
reclaimPolicy: Delete
volumeBindingMode: Immediate
```

### Add extra command line argument in cinder-csi-plugin container of cinder-csi-plugin deployments

```--cloud-name=region-one``` for region-one cinder-csi-plugin-region-one deployment

```--cloud-name=region-two``` for region-twoone cinder-csi-plugin-region-two deployment


### Add extra command line argument in cinder-csi-plugin container of cinder-csi-plugin deployments

```--cloud-name=region-one --additionnal-topology=topology.kubernetes.io/region=region-one``` for region-one cinder-csi-plugin-region-one daemonset

```--cloud-name=region-two --additionnal-topology=topology.kubernetes.io/region=region-two``` for region-two cinder-csi-plugin-region-two daemonset

