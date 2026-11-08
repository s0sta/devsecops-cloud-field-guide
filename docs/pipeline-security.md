# Pipeline security — source to runtime

Controls in the order a change travels through a delivery pipeline. Each one
lists the control, the usual tool and the failure mode it prevents.

## 1. Source

- **No secrets in git.** Block them before they land: `gitleaks` or
  `trufflehog` as a pre-commit hook and as a required CI check.
  Failure mode: a leaked token in history that survives even after a revert.
- **Protected branches.** Required reviews, required status checks, signed
  commits for release branches, no force-push.
- **Least-privilege CI identity.** Short-lived OIDC federation (GitHub Actions
  → cloud) instead of long-lived cloud keys stored as repository secrets.
  Failure mode: one compromised token owning the whole subscription.

## 2. Build

- **Dependency scanning.** `osv-scanner`, `pip-audit`, `npm audit`, Dependabot
  or Renovate for upgrades. Fail the build on high/critical.
- **Static analysis.** CodeQL or Semgrep with a ruleset that matches the
  language and the threat model.
- **IaC scanning.** `checkov`, `tfsec`/`trivy config`, `tflint` on every
  Terraform/Helm/Kubernetes change. Failure mode: a public storage account
  created by a "small" Terraform PR.
- **Reproducible builds.** Pin base images by digest, pin action versions to
  commit SHAs, lock dependency files.
- **Container hygiene.** `hadolint`, non-root user, distroless or minimal base,
  read-only root filesystem where possible.

## 3. Artifact

- **SBOM.** Generate with `syft` (or `cdxgen`) and attach it to the release.
- **Scan the image, not the repo.** `trivy image` / `grype` on the built
  artifact — the base image layer is part of your attack surface.
- **Sign.** `cosign sign` with a keyless Sigstore identity, or a managed key.
- **Provenance.** SLSA provenance from the build platform so consumers can
  verify what built the artifact and from which commit.
- **Immutable tags.** Deploy by digest, never by a mutable tag.

## 4. Deploy

- **GitOps.** The cluster state is a git commit: Argo CD or Flux reconcile it.
  No human `kubectl apply` in production.
- **Policy before admission.** Kyverno or OPA Gatekeeper: verify signatures,
  enforce non-root, block `latest`, require resource limits, allow registries
  from an allow-list.
- **Progressive delivery.** Canary or blue/green with automated rollback on
  error-rate or latency regression.

## 5. Runtime

- **Detect.** Falco or equivalent for runtime anomalies; ship events to the
  same logging stack as the audit logs.
- **Segregate.** Default-deny NetworkPolicies between workloads.
- **Audit.** Kubernetes audit logs, cloud activity logs and pipeline logs in
  one searchable place with retention that matches the compliance requirement.

## Minimum viable pipeline (if you only do five things)

1. Secret scanning on every push.
2. Dependency + IaC scanning in CI.
3. SBOM + image signing on release.
4. Admission control that rejects unsigned images.
5. Deploy by digest through GitOps, with an audit trail.
