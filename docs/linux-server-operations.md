# Linux server operations — triage and hardening

The runbook for "a server is behaving badly, find the cause" plus the baseline
every new Linux host should get before it takes traffic.

## Triage: what is actually wrong

Ask the machine in this order; each answer narrows the next question.

```sh
# Capacity and inodes — the classic silent killer
df -h; df -i
du -xhd1 / 2>/dev/null | sort -rh | head -20
journalctl --disk-usage

# Memory and pressure (OOM kills leave traces in the journal)
free -h
vmstat 1 5
journalctl -k --since "24 hours ago" | grep -iE "oom|killed process"

# Load vs CPU steal (a busy host and a noisy hypervisor look the same in top)
uptime; mpstat 1 3

# Services — failed units with their last log line
systemctl --failed
systemctl status <unit> -l --no-pager
journalctl -u <unit> --since "30 min ago" --no-pager | tail -40

# Network: who listens, who is connected, does DNS resolve
ss -tulpn
ss -tan state established | head
dig +short <name>; resolvectl status 2>/dev/null | head

# Time and TLS — skew breaks auth, certificates expire
timedatectl
openssl x509 -enddate -noout -in /etc/ssl/certs/<cert>.pem

# Kernel events and reboots
dmesg -T | tail -40
last -x | head
```

For a single command that walks all of the above read-only and explains each
finding, use **[nabd](https://github.com/s0sta/nabd)**.

## Hardening baseline

- **SSH**: keys only (`PasswordAuthentication no`, `PermitRootLogin no`),
  access through a bastion/VPN, `AllowUsers`/`AllowGroups` where possible.
- **Patching**: unattended security updates on, kernel livepatch or a monthly
  reboot window, and a documented reboot policy for kernel updates.
- **Accounts**: no shared logins, `sudo` for privilege, NOPASSWD off except
  for a reviewed automation account.
- **Audit**: `auditd` or the provider's audit agent installed; logs shipped
  off-host — a host that logs only to its own disk loses the evidence in the
  incident it is investigating.
- **Kernel/network sysctls**: syncookies, rp_filter, no IP forwarding unless
  the host routes, `kernel.kptr_restrict`, `kernel.dmesg_restrict`.
- **Journal limits**: `SystemMaxUse`/`MaxRetentionSec` set so logs cannot fill
  the root filesystem.
- **Services**: disable what you do not use (`cups`, `avahi`, `rpcbind`),
  bind management interfaces to localhost, fail2ban/sshguard only as defence
  in depth behind key-only SSH.
- **TLS**: automated renewal (certbot/acme.sh/ARC), a monitoring check on
  expiry at 21 and 7 days, and no self-signed certificate trusted by default.

## The five-minute check on a new host

```sh
hostnamectl; uptime
systemctl --failed
ss -tulpn
df -h; df -i
journalctl -p err --since "24 hours ago" --no-pager | tail -30
```
