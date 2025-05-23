---
title: "Restrict NGINX Ingress path values"
category: Security, NGINX Ingress
version: 1.6.0
subject: Ingress
policyType: "validate"
description: >
    This policy mitigates CVE-2021-25745 by restricting `spec.rules[].http.paths[].path` to safe values. Additional paths can be added as required. This issue has been fixed in NGINX Ingress v1.2.0.  Please refer to the CVE for details.
---

## Policy Definition
<a href="https://github.com/kyverno/policies/raw/main//nginx-ingress/restrict-ingress-paths/restrict-ingress-paths.yaml" target="-blank">/nginx-ingress/restrict-ingress-paths/restrict-ingress-paths.yaml</a>

```yaml
apiVersion: kyverno.io/v1
kind: ClusterPolicy
metadata:
  name: restrict-ingress-paths
  annotations:
    policies.kyverno.io/title: Restrict NGINX Ingress path values 
    policies.kyverno.io/category: Security, NGINX Ingress
    policies.kyverno.io/severity: high
    policies.kyverno.io/subject: Ingress
    policies.kyverno.io/minversion: "1.6.0"
    kyverno.io/kyverno-version: "1.6.0"
    kyverno.io/kubernetes-version: "1.23"
    policies.kyverno.io/description: >-
      This policy mitigates CVE-2021-25745 by restricting `spec.rules[].http.paths[].path` to safe values.
      Additional paths can be added as required. This issue has been fixed in NGINX Ingress v1.2.0. 
      Please refer to the CVE for details.
spec:
  validationFailureAction: Enforce
  rules:
    - name: check-paths
      match:
        any:
        - resources:
            kinds:
            - networking.k8s.io/v1/Ingress
      validate:
        message: "spec.rules[].http.paths[].path value is not allowed"
        deny:
          conditions:
            any:
            - key: "{{ request.object.spec.rules[].http.paths[].path.contains(@,'/etc') }}"
              operator: AnyIn
              value: [true]
            - key: "{{ request.object.spec.rules[].http.paths[].path.contains(@,'/var/run/secrets') }}"
              operator: AnyIn
              value: [true]
            - key: "{{ request.object.spec.rules[].http.paths[].path.contains(@,'/root') }}"
              operator: AnyIn
              value: [true]
            - key: "{{ request.object.spec.rules[].http.paths[].path.contains(@,'/var/run/kubernetes/serviceaccount') }}"
              operator: AnyIn
              value: [true]
            - key: "{{ request.object.spec.rules[].http.paths[].path.contains(@,'/etc/kubernetes/admin.conf') }}"
              operator: AnyIn
              value: [true]

```
