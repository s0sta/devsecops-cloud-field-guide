# Cloud baseline — Azure

A minimum security baseline for an Azure subscription that hosts real
workloads. It is written for Terraform-managed environments but applies to
portal-built ones too.

## Identity

- **Managed identities** for workloads (AKS workload identity, App Service,
  Functions, VMs). No service-principal secrets in pipelines.
- **No standing owner access.** Use PIM for just-in-time elevation; day-to-day
  accounts get Contributor on a resource group, not Owner on the subscription.
- **Conditional Access + MFA** for every human account, break-glass accounts
  excluded and monitored.
- **Least privilege at the data plane too.** Storage Blob Data Reader beats
  Contributor on the storage account.

## Network

- Private endpoints for storage, Key Vault, SQL, ACR; disable public network
  access on each (`public_network_access_enabled = false`).
- NSGs explicit deny for management ports from the internet; Azure Bastion or
  a VPN for admin access instead of public SSH/RDP.
- Egress: user-defined routes or a firewall if the compliance target requires
  it; otherwise at least log the flows you care about.
- AKS: private cluster for production, authorized IP ranges only for the API
  server if private is not possible.

## Data and secrets

- Encryption at rest is on by default — verify it, and use customer-managed
  keys where the data classification demands it.
- **Key Vault**: RBAC authorization mode, soft delete + purge protection,
  private endpoint, no secrets in app settings or pipeline variables.
- Backups tested, not just configured. A restore you have never run is a
  hope, not a backup.

## Detection and logging

- **Defender for Cloud** on the subscription, with the secure score reviewed
  weekly.
- Activity Log → Log Analytics workspace; diagnostic settings on every
  resource that supports them.
- Alert on: role assignment changes, Key Vault access anomalies, public
  storage exposure, NSG rule changes, sign-in risk.
- Retention matched to the compliance obligation, not to the default 30 days.

## Infrastructure as code

- One Terraform backend per environment: remote state in storage, state
  locking, no local state.
- `azurerm` provider pinned; plans reviewed in the PR, apply only from the
  pipeline with OIDC federation.
- Policy as code: Azure Policy assignments for the controls you would
  otherwise forget (allowed locations, required tags, TLS minimum version,
  no public IPs).

## Quick audit

```sh
# Resource groups / resources with public network access still enabled
az graph query -q "Resources | where isnotnull(properties.publicNetworkAccess)
  | project name, type, resourceGroup, properties.publicNetworkAccess"

# Storage accounts that allow public blob access
az graph query -q "Resources | where type =~ 'microsoft.storage/storageAccounts'
  | where properties.allowBlobPublicAccess == true
  | project name, resourceGroup"

# Subscription-level role assignments
az role assignment list --scope "/subscriptions/<subscription-id>" \
  --include-inherited --output table
```
