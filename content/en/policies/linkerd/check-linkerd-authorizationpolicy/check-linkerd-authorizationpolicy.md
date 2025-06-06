---
title: "Check Linkerd AuthorizationPolicy"
category: Linkerd
version: 
subject: AuthorizationPolicy
policyType: "validate"
description: >
    As of Linkerd 2.12, an AuthorizationPolicy is a resource used to selectively allow traffic to either a Server or HTTPRoute resource. Creating AuthorizationPolicies is needed when a Server exists in order to control what traffic is permitted within the mesh to the Pods selected by the Server or HTTPRoute. This policy, requiring Linkerd 2.12+, checks incoming AuthorizationPolicy resources to ensure that either a matching Server or HTTPRoute exists first.
---

## Policy Definition
<a href="https://github.com/kyverno/policies/raw/main//linkerd/check-linkerd-authorizationpolicy/check-linkerd-authorizationpolicy.yaml" target="-blank">/linkerd/check-linkerd-authorizationpolicy/check-linkerd-authorizationpolicy.yaml</a>

```yaml
apiVersion: kyverno.io/v1
kind: ClusterPolicy
metadata:
  name: check-linkerd-authorizationpolicy
  annotations:
    policies.kyverno.io/title: Check Linkerd AuthorizationPolicy
    policies.kyverno.io/category: Linkerd
    policies.kyverno.io/severity: medium
    policies.kyverno.io/subject: AuthorizationPolicy
    kyverno.io/kyverno-version: "1.8.0"
    kyverno.io/kubernetes-version: "1.24"
    policies.kyverno.io/description: >-
      As of Linkerd 2.12, an AuthorizationPolicy is a resource used to selectively allow traffic
      to either a Server or HTTPRoute resource. Creating AuthorizationPolicies is needed when
      a Server exists in order to control what traffic is permitted within the mesh to the Pods
      selected by the Server or HTTPRoute. This policy, requiring Linkerd 2.12+, checks incoming
      AuthorizationPolicy resources to ensure that either a matching Server or HTTPRoute exists
      first.
spec:
  validationFailureAction: Audit
  background: true
  rules:
  - name: check-server-exists
    match:
      any:
      - resources:
          kinds:
          - AuthorizationPolicy
    context:
    - name: servers
      apiCall:
        urlPath: "/apis/policy.linkerd.io/v1beta1/namespaces/{{request.namespace}}/servers"
        jmesPath: "items[].metadata.name || `[]`"
    - name: httproutes
      apiCall:
        urlPath: "/apis/policy.linkerd.io/v1beta1/namespaces/{{request.namespace}}/httproutes"
        jmesPath: "items[].metadata.name || `[]`"
    validate:
      message: "Server or HTTPRoute not found for this AuthorizationPolicy."
      deny:
        conditions:
          all:
          - key: "{{request.object.spec.targetRef.name}}"
            operator: AnyNotIn
            value: "{{servers}}"
          - key: "{{request.object.spec.targetRef.name}}"
            operator: AnyNotIn
            value: "{{httproutes}}"

```
