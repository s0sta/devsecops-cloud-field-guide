# Kubernetes hardening — baseline

A baseline that works on managed clusters (AKS/EKS/GKE) and on k3s alike.
Apply it as manifests or policies, then verify it with `kube-bench`.

## Pod Security Admission

- Label namespaces with the built-in policy level instead of hand-rolling
  security contexts everywhere:

```sh
kubectl label namespace production \
  pod-security.kubernetes.io/enforce=restricted \
  pod-security.kubernetes.io/warn=restricted \
  pod-security.kubernetes.io/audit=restricted
```

- Workloads must set `runAsNonRoot: true`, drop all capabilities, set
  `allowPrivilegeEscalation: false`, use a read-only root filesystem where
  possible, and never use `hostPath` or `hostNetwork` without a documented
  exception.

## RBAC

- No `cluster-admin` for workloads or CI.
- Roles scoped to a namespace and verbs; audit the dangerous verbs first:
  `create pods/exec`, `create pods/portforward`, `get secrets`, `create
  rolebindings`, `escalate`, `bind`, `impersonate`.
- Service accounts: one per workload, `automountServiceAccountToken: false`
  unless the pod actually calls the API.

```sh
kubectl get clusterrolebindings -o json | jq -r '
  .items[] | select(.subjects[]?.kind=="ServiceAccount") |
  "\(.metadata.name) -> \(.subjects[]?.name)"'
```

## Network

- Default-deny ingress and egress in every namespace, then allow explicitly.
- Egress restrictions prevent a compromised pod from reaching the internet or
  the metadata endpoint (`169.254.169.254`).

```sh
kubectl apply -f - <<'EOF'
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata: { name: default-deny, namespace: production }
spec: { podSelector: {}, policyTypes: [Ingress, Egress] }
EOF
```

## Admission policies

- Kyverno or Gatekeeper with a small, enforceable set:
  - images from an allow-listed registry, signed and deployed by digest;
  - no `:latest`;
  - resource requests and limits present;
  - no privileged pods; no host namespaces;
  - labels required for ownership and cost tracking.

## Control plane and node baseline

- Managed control plane: enable audit logs and ship them out.
- Nodes: patched by the provider or by your own update job; no SSH exposure
  to the internet; kubelet anonymous auth disabled.
- Secrets: external secret operator or CSI driver against Key Vault / Secrets
  Manager, not base64 blobs in git.

## Verify

```sh
# CIS-style checks for the distribution
kubectl apply -f https://raw.githubusercontent.com/aquasecurity/kube-bench/main/job.yaml
kubectl logs job/kube-bench

# What is actually running with elevated rights
kubectl get pods -A -o json | jq -r '.items[] |
  select(.spec.containers[].securityContext.privileged==true) |
  "\(.metadata.namespace)/\(.metadata.name)"'
```
