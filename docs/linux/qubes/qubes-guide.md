---
title: "Qubes OS Quickstart Guide"
sidebar_label: "Qubes Quickstart"
description: "A practical quickstart for Qubes OS: initial security posture, qube layout, update routine, and safe daily workflow."
slug: /linux/qubes/qubes-guide
---

# Qubes OS Quickstart Guide

This guide gives you a practical day-1 to day-7 setup path for Qubes OS.

## 1) Initial post-install checklist

- Complete first-boot Qubes setup wizard
- Apply dom0 updates:
  ```bash
  sudo qubes-dom0-update
  ```
- Update template VMs (Fedora/Debian templates)
- Confirm clock sync and network qube health

## 2) Create a clean qube layout

Use dedicated AppVMs by trust level:

- `work` (trusted org accounts)
- `personal` (personal browsing/email)
- `untrusted` (unknown files/sites)
- `vault` (offline secrets, no networking)

Keep tasks separated. Do not reuse the same qube across trust boundaries.

## 3) Networking and device isolation

- Keep `sys-net` and `sys-firewall` isolated
- Use `sys-usb` to isolate USB controllers
- Attach USB devices only to qubes that need them

## 4) Safe file movement rules

- Prefer copy (not move) when crossing trust levels
- Open risky files in disposable VMs (DispVM)
- Scan downloaded files in low-trust qube before transfer

## 5) Weekly maintenance routine

1. Update dom0
2. Update templates
3. Restart dependent AppVMs
4. Review active RPC policies
5. Check available disk and memory

## 6) Security guardrails

- Never browse the web from dom0
- Keep default-deny mindset for RPC policies
- Use short-lived disposable VMs for unknown content
- Maintain separate admin and daily workflows

## Useful commands

```bash
qvm-ls
qvm-prefs <vm-name>
qvm-start <vm-name>
qvm-shutdown <vm-name>
qvm-clone <source-vm> <new-vm>
```

---

If you want, I can add a next guide for **Qubes backup/restore and migration** (including `qvm-backup` operational runbook).
