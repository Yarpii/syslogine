---
sidebar_position: 4
title: "First Domain Controller Checklist"
sidebar_label: "First Domain Controller"
description: "Practical checklist for deploying the first Windows domain controller safely and with clean foundational settings."
slug: /windows/server/getting-started/first-domain-controller-checklist
---

# First Domain Controller Checklist

This runbook helps you stand up your first AD DS domain controller with clean foundations.

## Before promotion

- Reserve static IP and correct DNS settings
- Confirm accurate time sync source
- Choose domain naming standard (e.g., `corp.example.local`)
- Define OU strategy and admin delegation model

## Promotion essentials

1. Install AD DS role.
2. Promote server as first forest/domain controller.
3. Verify DNS zone creation.
4. Configure backup immediately after promotion.

## Post-promotion validation

```powershell
Get-ADDomain
Get-ADForest
dcdiag /v
repadmin /replsummary
```

## Security baseline for DCs

- Prohibit internet browsing from domain controllers
- Limit interactive logon rights
- Use tiered admin model for privileged access
- Apply strict patch window and change control

## Operational next steps

- Add a second DC for resilience
- Configure monitoring and alerting on AD/DNS health
- Document break-glass access procedure

