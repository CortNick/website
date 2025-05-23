---
title: "Require securityContext for Tekton TaskRun"
category: Tekton
version: 1.7.0
subject: TaskRun
policyType: "validate"
description: >
    A securityContext is required for each TaskRun step.
---

## Policy Definition
<a href="https://github.com/kyverno/policies/raw/main//tekton/require-tekton-securitycontext/require-tekton-securitycontext.yaml" target="-blank">/tekton/require-tekton-securitycontext/require-tekton-securitycontext.yaml</a>

```yaml
apiVersion: kyverno.io/v1
kind: ClusterPolicy
metadata:
  name: require-tekton-securitycontext
  annotations:
    policies.kyverno.io/title: Require securityContext for Tekton TaskRun
    policies.kyverno.io/category: Tekton
    policies.kyverno.io/severity: medium
    policies.kyverno.io/subject: TaskRun
    kyverno.io/kyverno-version: 1.7.2
    policies.kyverno.io/minversion: 1.7.0
    kyverno.io/kubernetes-version: "1.23"
    policies.kyverno.io/description: >- 
      A securityContext is required for each TaskRun step.
spec:
  validationFailureAction: Audit
  background: true
  rules:
  - name: check-step-securitycontext
    match:
      any:
      - resources:
          kinds:
          - tekton.dev/v1beta1/TaskRun.status
    validate:
      message: "A securityContext is required with `privileged` and `allowPrivilegeEscalation` set to `false`."
      pattern:
        =(status):
          =(taskSpec):
            steps:
              # TODO: missing securityContext for digest-to-results
              - (name): "!digest-to-results"
                securityContext:
                  # TODO: ideally all tasks run as non-root
                  #runAsNonRoot: true
                  privileged: false
                  allowPrivilegeEscalation: false
```
