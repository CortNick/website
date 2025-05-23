---
title: "Restrict Scale"
category: Other
version: 1.9.0
subject: Deployment
policyType: "validate"
description: >
    Pod controllers such as Deployments which implement replicas and permit the scale action use a `/scale` subresource to control this behavior. In addition to checks for creations of such controllers that their replica is in a certain shape, the scale operation and subresource needs to be accounted for as well. This policy, operable beginning in Kyverno 1.9, is a collection of rules which can be used to limit the replica count both upon creation of a Deployment and when a scale operation is performed.
---

## Policy Definition
<a href="https://github.com/kyverno/policies/raw/main//other/restrict-scale/restrict-scale.yaml" target="-blank">/other/restrict-scale/restrict-scale.yaml</a>

```yaml
apiVersion: kyverno.io/v2beta1
kind: ClusterPolicy
metadata:
  name: restrict-scale
  annotations:
    policies.kyverno.io/title: Restrict Scale
    policies.kyverno.io/category: Other
    policies.kyverno.io/severity: medium
    kyverno.io/kyverno-version: 1.9.0
    policies.kyverno.io/minversion: 1.9.0
    kyverno.io/kubernetes-version: "1.24"
    policies.kyverno.io/subject: Deployment
    policies.kyverno.io/description: >-
      Pod controllers such as Deployments which implement replicas and permit the scale action
      use a `/scale` subresource to control this behavior. In addition to checks for creations of
      such controllers that their replica is in a certain shape, the scale operation and subresource
      needs to be accounted for as well. This policy, operable beginning in Kyverno 1.9, is a collection
      of rules which can be used to limit the replica count both upon creation of a Deployment and
      when a scale operation is performed.
spec:
  validationFailureAction: Audit
  background: false
  rules:
  # This rule can be used to limit scale operations based upon Deployment labels assuming the given label
  # is also used as a selector.
  - name: scale-max-eight
    match:
      any:
      - resources:
          kinds:
          - Deployment/scale
    validate:
      message: The replica count for this Deployment may not exceed 8.
      pattern:
        (status):
          (selector): "*type=monitoring*"
        spec:
          replicas: <9
  # This rule can be used for more advanced decision making, for example limiting scale based
  # upon Deployment annotations which are not sent by the API server to admission controllers
  # when a scale is performed.
  - name: scale-max-eight-annotations
    match:
      any:
      - resources:
          kinds:
          - Deployment/scale
    context:
      - name: parentdeploy
        apiCall:
          urlPath: "/apis/apps/v1/namespaces/{{request.namespace}}/deployments?fieldSelector=metadata.name={{request.name}}"
          jmesPath: "items[0]"
      - name: dept
        variable:
          jmesPath: parentdeploy.metadata.annotations."corp.org/dept"
          default: empty
    validate:
      message: The replica count for this Deployment may not exceed 8.
      deny:
        conditions:
          all:
          - key: "{{dept}}"
            operator: Equals
            value: engineering
          - key: "{{request.object.spec.replicas}}"
            operator: GreaterThan
            value: 8
  # This rule, which is a simple check on Deployments for replicas (not scaling them) can be used
  # to complement scale operations. This may be needed along with at least one of the prior two rules
  # to fully limit the number of total replicas allowed. For example, this rule would limit creation of
  # Deployments to no more than 4 replicas, without an additional rule for scaling it would not prevent
  # scaling over 4. By combining this CREATE rule with one of the scale rules above, a cluster admin
  # may effectively provide an allowed range of replicas good for both day1 and day2 operations.
  - name: create-max-four
    match:
      any:
      - resources:
          kinds:
          - Deployment
          selector:
            matchLabels:
              type: monitoring
    validate:
      message: The replica count for this Deployment may not exceed 4.
      pattern:
        spec:
          replicas: <5

```
