---
title: "Restrict Auto-Mount of Service Account Tokens in Service Account in CEL expressions"
category: Security in CEL
version: 
subject: Secret,ServiceAccount
policyType: "validate"
description: >
    Kubernetes automatically mounts ServiceAccount credentials in each ServiceAccount. The ServiceAccount may be assigned roles allowing Pods to access API resources. Blocking this ability is an extension of the least privilege best practice and should be followed if Pods do not need to speak to the API server to function. This policy ensures that mounting of these ServiceAccount tokens is blocked.      
---

## Policy Definition
<a href="https://github.com/kyverno/policies/raw/main//other-cel/restrict-sa-automount-sa-token/restrict-sa-automount-sa-token.yaml" target="-blank">/other-cel/restrict-sa-automount-sa-token/restrict-sa-automount-sa-token.yaml</a>

```yaml
apiVersion: kyverno.io/v1
kind: ClusterPolicy
metadata:
  name: restrict-sa-automount-sa-token
  annotations:
    policies.kyverno.io/title: Restrict Auto-Mount of Service Account Tokens in Service Account in CEL expressions
    policies.kyverno.io/category: Security in CEL 
    kyverno.io/kyverno-version: 1.11.0
    kyverno.io/kubernetes-version: "1.26-1.27"
    policies.kyverno.io/severity: medium
    policies.kyverno.io/subject: Secret,ServiceAccount
    policies.kyverno.io/description: >-
      Kubernetes automatically mounts ServiceAccount credentials in each ServiceAccount.
      The ServiceAccount may be assigned roles allowing Pods to access API resources.
      Blocking this ability is an extension of the least privilege best practice and should
      be followed if Pods do not need to speak to the API server to function.
      This policy ensures that mounting of these ServiceAccount tokens is blocked.      
spec:
  validationFailureAction: Audit
  background: true
  rules:
  - name: validate-sa-automountServiceAccountToken
    match:
      any:
      - resources:
          kinds:
          - ServiceAccount
          operations:
          - CREATE
          - UPDATE
    validate:
      cel:
        expressions:
          - expression: "object.?automountServiceAccountToken.orValue(true) == false"
            message: "ServiceAccounts must set automountServiceAccountToken to false."


```
