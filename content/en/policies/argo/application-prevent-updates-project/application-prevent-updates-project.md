---
title: "Prevent Updates to Project"
category: Argo
version: 1.6.0
subject: Application
policyType: "validate"
description: >
    This policy prevents updates to the project field after an Application is created.
---

## Policy Definition
<a href="https://github.com/kyverno/policies/raw/main//argo/application-prevent-updates-project/application-prevent-updates-project.yaml" target="-blank">/argo/application-prevent-updates-project/application-prevent-updates-project.yaml</a>

```yaml
apiVersion: kyverno.io/v1
kind: ClusterPolicy
metadata:
  name: application-prevent-updates-project
  annotations:
    policies.kyverno.io/title: Prevent Updates to Project
    policies.kyverno.io/category: Argo
    policies.kyverno.io/severity: medium
    kyverno.io/kyverno-version: 1.6.2
    policies.kyverno.io/minversion: 1.6.0
    kyverno.io/kubernetes-version: "1.23"
    policies.kyverno.io/subject: Application
    policies.kyverno.io/description: >-
      This policy prevents updates to the project field after an Application is created.
spec:
  validationFailureAction: Audit
  background: true
  rules:
    - name: project-updates
      match:
        any:
        - resources:
            kinds:
              - Application
      preconditions:
        all:
        - key: "{{ request.operation || 'BACKGROUND' }}"
          operator: Equals
          value: UPDATE
      validate:
        message: "The spec.project cannot be changed once the Application is created."
        deny:
          conditions:
            any:
            - key: "{{request.object.spec.project}}"
              operator: NotEquals
              value: "{{request.oldObject.spec.project}}"
```
