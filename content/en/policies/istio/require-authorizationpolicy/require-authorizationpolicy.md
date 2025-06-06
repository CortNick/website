---
title: "Require Istio AuthorizationPolicies"
category: Istio
version: 1.6.0
subject: AuthorizationPolicy
policyType: "validate"
description: >
    An AuthorizationPolicy is used to provide access controls for traffic in the mesh and can be defined at multiple levels. For the Namespace level, all Namespaces should have at least one AuthorizationPolicy. This policy, designed to run in background mode for reporting purposes, ensures every Namespace has at least one AuthorizationPolicy.
---

## Policy Definition
<a href="https://github.com/kyverno/policies/raw/main//istio/require-authorizationpolicy/require-authorizationpolicy.yaml" target="-blank">/istio/require-authorizationpolicy/require-authorizationpolicy.yaml</a>

```yaml
apiVersion: kyverno.io/v1
kind: ClusterPolicy
metadata:
  name: require-authorizationpolicies
  annotations:
    policies.kyverno.io/title: Require Istio AuthorizationPolicies
    policies.kyverno.io/category: Istio
    policies.kyverno.io/severity: medium
    kyverno.io/kyverno-version: 1.8.0
    policies.kyverno.io/minversion: 1.6.0
    kyverno.io/kubernetes-version: "1.24"
    policies.kyverno.io/subject: AuthorizationPolicy
    policies.kyverno.io/description: >-
      An AuthorizationPolicy is used to provide access controls for traffic in the mesh and
      can be defined at multiple levels. For the Namespace level, all Namespaces should have
      at least one AuthorizationPolicy. This policy, designed to run in background mode for reporting
      purposes, ensures every Namespace has at least one AuthorizationPolicy.
spec:
  validationFailureAction: Audit
  background: true
  rules:
  - name: check-authz-pol
    match:
      any:
      - resources:
          kinds:
          - Namespace
    context:
    - name: allauthorizationpolicies
      apiCall:
        urlPath: "/apis/security.istio.io/v1beta1/authorizationpolicies"
        jmesPath: "items[].metadata.namespace"
    validate:
      message: "All Namespaces must have an AuthorizationPolicy."
      deny:
        conditions:
          all:
          - key: "{{request.object.metadata.name}}"
            operator: AnyNotIn
            value: "{{allauthorizationpolicies}}"

```
