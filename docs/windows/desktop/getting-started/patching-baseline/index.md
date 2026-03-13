---
sidebar_position: 3
title: "Windows Desktop Patching Baseline"
sidebar_label: "Patching Baseline"
description: "A practical patch-management baseline for Windows 10/11 desktops in community or small business environments."
slug: /windows/desktop/getting-started/patching-baseline
---

# Windows Desktop Patching Baseline

Use this guide to keep Windows endpoints current without disrupting users.

## Patch policy model

- **Pilot ring**: 5-10% of devices (IT and test users)
- **General ring**: Remaining managed devices
- **Emergency path**: Fast-track critical security patches

## Weekly patch routine

1. Review released updates and known issues.
2. Deploy to pilot ring first.
3. Monitor for 24-48 hours.
4. Deploy to general ring.
5. Confirm reboot completion + health checks.

## Useful checks (PowerShell)

```powershell
Get-HotFix | Sort-Object InstalledOn -Descending | Select-Object -First 10
Get-ComputerInfo | Select-Object WindowsProductName,WindowsVersion,OsBuildNumber
```

## Recommended operational controls

- Set active hours to avoid surprise restarts.
- Enforce reboot deadlines for security updates.
- Track patch compliance by device group.
- Keep rollback guidance for problematic updates.

## Troubleshooting quick wins

- Failed updates: clear SoftwareDistribution cache and retry.
- Reboot loops: roll back latest quality update.
- Drifted devices: re-enroll into your management baseline.

