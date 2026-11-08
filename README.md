# DevOps · Cloud · DevSecOps — field guide

Practical, production-tested notes on running systems where delivery speed and
security are not a trade-off: secure CI/CD pipelines, hardened Kubernetes,
guardrailed cloud infrastructure, and Linux servers that stay up.

Every page here is a checklist or a runbook you can copy into a real
environment — the control, the command, and the reason it exists. No affiliate
links, no tool-of-the-week, no theory that collapses on the first incident.

## Inside

| Page | What it covers |
| --- | --- |
| [Pipeline security](docs/pipeline-security.md) | Source → build → artifact → deploy: secrets, SCA/SAST, IaC scanning, SBOM, image signing, provenance, admission control |
| [Kubernetes hardening](docs/kubernetes-hardening.md) | Pod Security Admission, RBAC, default-deny NetworkPolicy, admission policies, kube-bench baseline |
| [Cloud baseline — Azure](docs/cloud-baseline-azure.md) | Managed identities, private networking, Key Vault, Defender for Cloud, policy-as-code, Terraform state |
| [Linux server operations](docs/linux-server-operations.md) | Triage runbook, hardening, patching, certificate hygiene, journal limits |

## Golden rules

1. **Every change goes through the pipeline.** No direct pushes to a production
   branch, no manual `docker push`, no console click that is not also in code.
2. **Trust nothing you did not build and verify.** Scan dependencies, sign
   artifacts, attach provenance, and verify at admission — not after deployment.
3. **Least privilege in every layer.** Cloud identity, Kubernetes RBAC, CI
   tokens, database users. Short-lived credentials beat long-lived secrets.
4. **If it is not logged and alerted, it did not happen.** Audit logs, pipeline
   logs, admission decisions and cloud activity go to one place.
5. **Rollback is a feature.** The pipeline is not done until a bad release can
   be reverted in one action.

## Related projects

- **[nabd](https://github.com/s0sta/nabd)** — a doctor for Linux servers: one
  read-only command finds what is wrong, explains why, and prints the fix.
- **[secure-delivery-pipeline](https://github.com/s0sta/secure-delivery-pipeline)**
  — a reference pipeline with signed images, SBOM + SLSA provenance, Argo CD
  GitOps and Kyverno admission control.
- **[azure-k3s-platform](https://github.com/s0sta/azure-k3s-platform)** —
  Terraform-built Azure landing zone with a k3s cluster.

## Contributing

Issues and pull requests are welcome. Keep changes practical: a control without
a command and a failure mode is not finished.

## License

MIT — see [LICENSE](LICENSE).

## Made by s0sta

Built and maintained by **s0sta** — IT Manager and DevSecOps engineer in Riyadh.

More notes, study pages and projects: [s0sta.com](https://s0sta.com) ·
[LinkedIn](https://www.linkedin.com/in/hosam-hani-abdelati/) ·
[X](https://x.com/s0sta_)
