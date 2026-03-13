---
sidebar_position: 4
title: "Windows Desktop Security Baseline"
sidebar_label: "Security Baseline"
description: "A starter security baseline for Windows desktop endpoints: identity, encryption, firewall, and audit posture."
slug: /windows/desktop/getting-started/security-baseline
---

# Windows Desktop Security Baseline

This baseline is designed for managed Windows 10/11 endpoints.

## Core controls

- Enforce MFA for admin-capable users
- Require Microsoft Defender Antivirus + realtime protection
- Enable Windows Firewall on all profiles
- Enforce BitLocker on supported hardware
- Disable local admin for standard users

## Identity and access

- Use named admin accounts (no shared credentials)
- Remove dormant local accounts
- Restrict local Administrators group membership

## Device posture checks

```powershell
Get-MpComputerStatus | Select-Object AntivirusEnabled,RealTimeProtectionEnabled
manage-bde -status
netsh advfirewall show allprofiles
```

## Logging and response readiness

- Keep endpoint logs available for at least 30 days
- Alert on repeated failed logon events
- Document isolation workflow for suspected compromise

## Minimum review cadence

- Weekly: malware detections + failed logons
- Monthly: local admin membership and firewall profile validation
- Quarterly: baseline review and policy updates

