---
title: "Require Read-Only Root Filesystem in CEL expressions"
category: Best Practices, EKS Best Practices, PSP Migration in CEL
version: 1.11.0
subject: Pod
policyType: "validate"
description: >
    A read-only root file system helps to enforce an immutable infrastructure strategy; the container only needs to write on the mounted volume that persists the state. An immutable root filesystem can also prevent malicious binaries from writing to the host system. This policy validates that containers define a securityContext with `readOnlyRootFilesystem: true`.
---

## Policy Definition
<a href="https://github.com/kyverno/policies/raw/main//best-practices-cel/require-ro-rootfs/require-ro-rootfs.yaml" target="-blank">/best-practices-cel/require-ro-rootfs/require-ro-rootfs.yaml</a>

```yaml
apiVersion: kyverno.io/v1
kind: ClusterPolicy
metadata:
  name: require-ro-rootfs
  annotations:
    policies.kyverno.io/title: Require Read-Only Root Filesystem in CEL expressions
    policies.kyverno.io/category: Best Practices, EKS Best Practices, PSP Migration in CEL 
    policies.kyverno.io/severity: medium
    policies.kyverno.io/subject: Pod
    kyverno.io/kubernetes-version: "1.26-1.27"
    policies.kyverno.io/minversion: 1.11.0
    policies.kyverno.io/description: >-
      A read-only root file system helps to enforce an immutable infrastructure strategy;
      the container only needs to write on the mounted volume that persists the state.
      An immutable root filesystem can also prevent malicious binaries from writing to the
      host system. This policy validates that containers define a securityContext
      with `readOnlyRootFilesystem: true`.
spec:
  validationFailureAction: Audit
  background: true
  rules:
  - name: validate-readOnlyRootFilesystem
    match:
      any:
      - resources:
          kinds:
          - Pod
          operations:
          - CREATE
          - UPDATE
    validate:
      cel:
        expressions:
          - expression: >-
              object.spec.containers.all(container,
              container.?securityContext.?readOnlyRootFilesystem.orValue(false) == true)
            message: "Root filesystem must be read-only."
          

```
