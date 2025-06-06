---
title: "Restrict Image Registries"
category: Best Practices, EKS Best Practices
version: 1.6.0
subject: Pod
policyType: "validate"
description: >
    Images from unknown, public registries can be of dubious quality and may not be scanned and secured, representing a high degree of risk. Requiring use of known, approved registries helps reduce threat exposure by ensuring image pulls only come from them. This policy validates that container images only originate from the registry `eu.foo.io` or `bar.io`. Use of this policy requires customization to define your allowable registries.
---

## Policy Definition
<a href="https://github.com/kyverno/policies/raw/main//best-practices/restrict-image-registries/restrict-image-registries.yaml" target="-blank">/best-practices/restrict-image-registries/restrict-image-registries.yaml</a>

```yaml
apiVersion: kyverno.io/v1
kind: ClusterPolicy
metadata:
  name: restrict-image-registries
  annotations:
    policies.kyverno.io/title: Restrict Image Registries
    policies.kyverno.io/category: Best Practices, EKS Best Practices
    policies.kyverno.io/severity: medium
    policies.kyverno.io/minversion: 1.6.0
    kyverno.io/kubernetes-version: "1.26"
    policies.kyverno.io/subject: Pod
    policies.kyverno.io/description: >-
      Images from unknown, public registries can be of dubious quality and may not be
      scanned and secured, representing a high degree of risk. Requiring use of known, approved
      registries helps reduce threat exposure by ensuring image pulls only come from them. This
      policy validates that container images only originate from the registry `eu.foo.io` or
      `bar.io`. Use of this policy requires customization to define your allowable registries.
spec:
  validationFailureAction: Audit
  background: true
  rules:
  - name: validate-registries
    match:
      any:
      - resources:
          kinds:
          - Pod
    validate:
      message: "Unknown image registry."
      pattern:
        spec:
          =(ephemeralContainers):
          - image: "eu.foo.io/* | bar.io/*"
          =(initContainers):
          - image: "eu.foo.io/* | bar.io/*"
          containers:
          - image: "eu.foo.io/* | bar.io/*"

```
