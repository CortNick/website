---
title: "Enforce Istio Ambient Mode"
category: Istio
version: 1.6.0
subject: Namespace
policyType: "validate"
description: >
    In order for Istio to include namespaces in ambient mode, the label `istio.io/dataplane-mode` must be set to `ambient`. This policy ensures that all new Namespaces set `istio.io/dataplane-mode` to `ambient`.
---

## Policy Definition
<a href="https://github.com/kyverno/policies/raw/main//istio/enforce-ambient-mode-namespace/enforce-ambient-mode-namespace.yaml" target="-blank">/istio/enforce-ambient-mode-namespace/enforce-ambient-mode-namespace.yaml</a>

```yaml
apiVersion: kyverno.io/v1
kind: ClusterPolicy
metadata:
  name: enforce-ambient-mode-namespace
  annotations:
    policies.kyverno.io/title: Enforce Istio Ambient Mode
    policies.kyverno.io/category: Istio
    policies.kyverno.io/severity: medium
    kyverno.io/kyverno-version: 1.8.0
    policies.kyverno.io/minversion: 1.6.0
    kyverno.io/kubernetes-version: "1.24"
    policies.kyverno.io/subject: Namespace
    policies.kyverno.io/description: >-
      In order for Istio to include namespaces in ambient mode, the label
      `istio.io/dataplane-mode` must be set to `ambient`. This policy ensures that all new Namespaces
      set `istio.io/dataplane-mode` to `ambient`.
spec:
  validationFailureAction: Audit
  background: true
  rules:
  - name: check-amblient-mode-enabled
    match:
      any:
      - resources:
          kinds:
          - Namespace
    validate:
      message: "All new Namespaces must have Istio ambient mode enabled."
      pattern:
        metadata:
          labels:
            istio.io/dataplane-mode: ambient

```
