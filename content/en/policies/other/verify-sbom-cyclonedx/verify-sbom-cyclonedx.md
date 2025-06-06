---
title: "Verify CycloneDX SBOM (Keyless)"
category: Software Supply Chain Security
version: 1.8.3
subject: Pod
policyType: "verifyImages"
description: >
    Software Bill of Materials (SBOM) provide details on the composition of a given container image and may be represented in a couple different standards. Having an SBOM can be important to ensuring images are built using verified processes. This policy verifies that an image has an SBOM in CycloneDX format and was signed by the expected subject and issuer when produced through GitHub Actions and using Cosign's keyless signing. It requires configuration based upon your own values.
---

## Policy Definition
<a href="https://github.com/kyverno/policies/raw/main//other/verify-sbom-cyclonedx/verify-sbom-cyclonedx.yaml" target="-blank">/other/verify-sbom-cyclonedx/verify-sbom-cyclonedx.yaml</a>

```yaml
apiVersion: kyverno.io/v1
kind: ClusterPolicy
metadata:
  name: verify-sbom-cyclonedx
  annotations:
    policies.kyverno.io/title: Verify CycloneDX SBOM (Keyless)
    policies.kyverno.io/category: Software Supply Chain Security
    policies.kyverno.io/severity: medium
    policies.kyverno.io/subject: Pod
    policies.kyverno.io/minversion: 1.8.3
    kyverno.io/kyverno-version: 1.9.0
    kyverno.io/kubernetes-version: "1.24"
    policies.kyverno.io/description: >-
      Software Bill of Materials (SBOM) provide details on the composition of a given
      container image and may be represented in a couple different standards.
      Having an SBOM can be important to ensuring images are built using verified
      processes. This policy verifies that an image has an SBOM in CycloneDX format
      and was signed by the expected subject and issuer when produced through GitHub Actions
      and using Cosign's keyless signing. It requires configuration based upon your own values.
spec:
  validationFailureAction: Audit
  webhookTimeoutSeconds: 30
  rules:
    - name: check-sbom
      match:
        any:
        - resources:
            kinds:
              - Pod
      verifyImages:
      - imageReferences:
        - "myreg.org/path/repo:*"
        attestations:
        - predicateType: https://cyclonedx.org/schema
          attestors:
          - entries:
            - keyless:
                subject: "mysubject"
                issuer: "https://token.actions.githubusercontent.com"
                rekor:
                  url: https://rekor.sigstore.dev
          conditions:
          - all:
            - key: "{{ Data.bomFormat }}"
              operator: Equals
              value: CycloneDX
```
