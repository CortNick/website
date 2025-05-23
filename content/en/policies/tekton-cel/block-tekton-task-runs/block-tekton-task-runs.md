---
title: "Block Tekton TaskRun in CEL expressions"
category: Tekton in CEL
version: 1.11.0
subject: TaskRun
policyType: "validate"
description: >
    Restrict creation of TaskRun resources to the Tekton pipelines controller.
---

## Policy Definition
<a href="https://github.com/kyverno/policies/raw/main//tekton-cel/block-tekton-task-runs/block-tekton-task-runs.yaml" target="-blank">/tekton-cel/block-tekton-task-runs/block-tekton-task-runs.yaml</a>

```yaml
apiVersion: kyverno.io/v1
kind: ClusterPolicy
metadata:
  name: block-tekton-task-runs
  annotations:
    policies.kyverno.io/title: Block Tekton TaskRun in CEL expressions
    policies.kyverno.io/category: Tekton in CEL 
    policies.kyverno.io/severity: medium
    policies.kyverno.io/subject: TaskRun
    kyverno.io/kyverno-version: 1.11.0
    policies.kyverno.io/minversion: 1.11.0
    kyverno.io/kubernetes-version: "1.26-1.27"
    policies.kyverno.io/description: >-
      Restrict creation of TaskRun resources to the Tekton pipelines controller.
spec:
  validationFailureAction: Audit
  background: false
  rules:
  - name: check-taskrun-user
    match:
      any:
      - resources:
          kinds:
            - TaskRun
          operations:
          - CREATE
          - UPDATE
    exclude:
      any:
      - subjects:
        - kind: User 
          name: "system:serviceaccount:tekton-pipelines:tekton-pipelines-controller"
    validate:
      cel:
        expressions:
          - expression: "false"
            message: Creating a TaskRun is not allowed.


```
