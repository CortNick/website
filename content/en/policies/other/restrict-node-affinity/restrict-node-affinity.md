---
title: "Restrict Node Affinity"
category: Other
version: 
subject: Pod
policyType: "validate"
description: >
    Pods may use several mechanisms to prefer scheduling on a set of nodes, and nodeAffinity is one of them. nodeAffinity uses expressions to select eligible nodes for scheduling decisions and may override intended placement options by cluster administrators. This policy ensures that nodeAffinity is not used in a Pod spec.
---

## Policy Definition
<a href="https://github.com/kyverno/policies/raw/main//other/restrict-node-affinity/restrict-node-affinity.yaml" target="-blank">/other/restrict-node-affinity/restrict-node-affinity.yaml</a>

```yaml
apiVersion: kyverno.io/v1
kind: ClusterPolicy
metadata:
  name: restrict-node-affinity
  annotations:
    policies.kyverno.io/title: Restrict Node Affinity
    policies.kyverno.io/category: Other
    policies.kyverno.io/severity: medium
    policies.kyverno.io/subject: Pod
    kyverno.io/kyverno-version: 1.8.4
    kyverno.io/kubernetes-version: "1.24"
    policies.kyverno.io/description: >-
      Pods may use several mechanisms to prefer scheduling on a set of nodes,
      and nodeAffinity is one of them. nodeAffinity uses expressions to select
      eligible nodes for scheduling decisions and may override intended placement
      options by cluster administrators. This policy ensures that nodeAffinity
      is not used in a Pod spec.
spec:
  background: true
  validationFailureAction: Audit
  rules:
  - name: check-nodeaffinity
    match:
      any:
      - resources:
          kinds:
          - Pod
    validate:
      message: "Node affinity cannot be used."
      pattern:
        spec:
          =(affinity):
            X(nodeAffinity): "null"
```
