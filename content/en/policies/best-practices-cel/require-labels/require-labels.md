---
title: "Require Labels in CEL expressions"
category: Best Practices in CEL
version: 1.11.0
subject: Pod, Label
policyType: "validate"
description: >
    Define and use labels that identify semantic attributes of your application or Deployment. A common set of labels allows tools to work collaboratively, describing objects in a common manner that all tools can understand. The recommended labels describe applications in a way that can be queried. This policy validates that the label `app.kubernetes.io/name` is specified with some value.
---

## Policy Definition
<a href="https://github.com/kyverno/policies/raw/main//best-practices-cel/require-labels/require-labels.yaml" target="-blank">/best-practices-cel/require-labels/require-labels.yaml</a>

```yaml
apiVersion: kyverno.io/v1
kind: ClusterPolicy
metadata:
  name: require-labels
  annotations:
    policies.kyverno.io/title: Require Labels in CEL expressions
    policies.kyverno.io/category: Best Practices in CEL 
    policies.kyverno.io/minversion: 1.11.0
    kyverno.io/kubernetes-version: "1.26-1.27"
    policies.kyverno.io/severity: medium
    policies.kyverno.io/subject: Pod, Label
    policies.kyverno.io/description: >-
      Define and use labels that identify semantic attributes of your application or Deployment.
      A common set of labels allows tools to work collaboratively, describing objects in a common manner that
      all tools can understand. The recommended labels describe applications in a way that can be
      queried. This policy validates that the label `app.kubernetes.io/name` is specified with some value.
spec:
  validationFailureAction: Audit
  background: true
  rules:
  - name: check-for-labels
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
              object.metadata.?labels[?'app.kubernetes.io/name'].orValue('') != ""
            message: "The label `app.kubernetes.io/name` is required." 


```
