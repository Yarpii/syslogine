---
sidebar_position: 2
title: "Windows Desktop Getting Started"
sidebar_label: "Getting Started"
description: "Start here for Windows desktop administration: setup checklist, security baseline, updates, apps, and support workflow."
slug: /windows/desktop/getting-started
---

# Windows Desktop Getting Started

This guide is for **Windows 10/11 desktop administration** (laptops, workstations, and user endpoints).

## Scope (Desktop only)

Use this track for:

- User devices and endpoint policies
- Office productivity apps and local tooling
- Driver/peripheral troubleshooting
- Endpoint security and patching

If you need AD DS, DNS, DHCP, Hyper-V host planning, or server role management, use the **Windows Server** track instead.

## Day-0 checklist for a new endpoint

1. Confirm edition and activation:
   ```powershell
   winver
   slmgr /xpr
   ```
2. Apply all updates:
   ```powershell
   Get-WindowsUpdateLog
   ```
   (or use Settings > Windows Update)
3. Join Entra ID / AD domain if required by policy.
4. Enable endpoint protection (Microsoft Defender + firewall).
5. Enforce disk encryption (BitLocker where supported).
6. Install approved baseline apps.

## Recommended baseline controls

- Standard user accounts for daily work
- MFA for admin-capable identities
- Controlled local admin membership
- Screen lock and idle timeout policy
- Browser hardening + extension policy

## Operational routines

- Weekly patch review and update validation
- Monthly software inventory cleanup
- Driver/firmware updates on hardware cycle
- Endpoint health checks (disk, memory, security state)

## Next guides

- [Windows 10 documentation](/docs/windows/desktop/windows-10)
- [Windows 11 documentation](/docs/windows/desktop/windows-11)
- [Enterprise desktop management](/docs/windows/desktop/enterprise)
