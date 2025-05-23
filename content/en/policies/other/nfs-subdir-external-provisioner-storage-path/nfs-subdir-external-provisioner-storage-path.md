---
title: "NFS Subdirectory External Provisioner Enforce Storage Path"
category: Other
version: 1.6.0
subject: PersistentVolumeClaim
policyType: "validate"
description: >
    The NFS subdir external provisioner project allows defining a StorageClass with a pathPattern, a template used to provision subdirectories on NFS exports. This can be controlled with an annotation on a PVC called `nfs.io/storage-path`. This policy ensures that if the StorageClass name `nfs-client` is used by a PVC, corresponding to the NFS subdir external provisioner, and if it sets the nfs.io/storage-path annotation that it cannot be empty, which may otherwise result in it consuming the root of the designated path.
---

## Policy Definition
<a href="https://github.com/kyverno/policies/raw/main//other/nfs-subdir-external-provisioner-storage-path/nfs-subdir-external-provisioner-storage-path.yaml" target="-blank">/other/nfs-subdir-external-provisioner-storage-path/nfs-subdir-external-provisioner-storage-path.yaml</a>

```yaml
apiVersion: kyverno.io/v1
kind: ClusterPolicy
metadata:
  name: nfs-subdir-external-provisioner-storage-path
  annotations:
    policies.kyverno.io/title: NFS Subdirectory External Provisioner Enforce Storage Path
    policies.kyverno.io/category: Other
    policies.kyverno.io/severity: medium
    policies.kyverno.io/subject: PersistentVolumeClaim
    kyverno.io/kyverno-version: 1.6.2
    policies.kyverno.io/minversion: 1.6.0
    kyverno.io/kubernetes-version: "1.23"
    policies.kyverno.io/description: >-
      The NFS subdir external provisioner project allows defining a StorageClass with a pathPattern,
      a template used to provision subdirectories on NFS exports. This can be controlled with an
      annotation on a PVC called `nfs.io/storage-path`. This policy ensures that if the StorageClass
      name `nfs-client` is used by a PVC, corresponding to the NFS subdir external provisioner, and if it sets the nfs.io/storage-path
      annotation that it cannot be empty, which may otherwise result in it consuming the root of the designated path.
spec:
  background: false
  validationFailureAction: Audit
  rules:
  - name: enforce-storage-path
    match:
      any:
      - resources:
          kinds:
          - PersistentVolumeClaim
    preconditions:
      all:
      - key: "{{request.operation || 'BACKGROUND'}}"
        operator: AnyIn
        value:
        - CREATE
        - UPDATE
    validate:
      message: nfs.io/storage-path annotation must not be empty.
      pattern:
        metadata:
          =(annotations):
            =(nfs.io/storage-path): "?*"
        spec:
          <(storageClassName): nfs-client

```
