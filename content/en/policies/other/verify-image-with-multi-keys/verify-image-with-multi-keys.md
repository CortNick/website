---
title: "Verify Image with Multiple Keys"
category: Software Supply Chain Security
version: 1.7.0
subject: Pod
policyType: "verifyImages"
description: >
    There may be multiple keys used to sign images based on the parties involved in the creation process. This image verification policy requires the named image be signed by two separate keys. It will search for a global "production" key in a ConfigMap called `keys` in the `default` Namespace and also a Namespace key in the same ConfigMap.
---

## Policy Definition
<a href="https://github.com/kyverno/policies/raw/main//other/verify-image-with-multi-keys/verify-image-with-multi-keys.yaml" target="-blank">/other/verify-image-with-multi-keys/verify-image-with-multi-keys.yaml</a>

```yaml
apiVersion: kyverno.io/v1
kind: ClusterPolicy
metadata:
  name: verify-image-with-multi-keys
  annotations:
    policies.kyverno.io/title: Verify Image with Multiple Keys
    policies.kyverno.io/category: Software Supply Chain Security
    policies.kyverno.io/severity: medium
    policies.kyverno.io/subject: Pod
    policies.kyverno.io/minversion: 1.7.0
    kyverno.io/kyverno-version: 1.7.2
    kyverno.io/kubernetes-version: "1.23"
    policies.kyverno.io/description: >-
      There may be multiple keys used to sign images based on
      the parties involved in the creation process. This image
      verification policy requires the named image be signed by
      two separate keys. It will search for a global "production"
      key in a ConfigMap called `keys` in the `default` Namespace
      and also a Namespace key in the same ConfigMap.
spec:
  validationFailureAction: Enforce
  background: false
  rules:
    - name: check-image-with-two-keys
      match:
        any:
        - resources:
            kinds:
              - Pod
      context:
      - name: keys
        configMap:
          name: keys
          namespace: default
      verifyImages:
      - imageReferences:
        - "ghcr.io/myorg/myimage*"
        required: true
        attestors:
        - count: 2
          entries:
          - keys: 
              publicKeys: "{{ keys.data.production }}"
          - keys: 
              publicKeys: "{{ keys.data.{{request.namespace}} }}"

```
