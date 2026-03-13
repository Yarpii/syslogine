---
sidebar_position: 3
title: "Windows Member Server Hardening Guide"
sidebar_label: "Member Server Hardening"
description: "Hardening checklist for Windows member servers before production rollout."
slug: /windows/server/getting-started/member-server-hardening
---

# Windows Member Server Hardening Guide

Apply this checklist after OS install and before application go-live.

## 1) Identity and privilege controls

- Use dedicated admin identities (no daily-use admin accounts)
- Restrict local Administrators membership
- Use service accounts with least privilege

## 2) Network exposure

- Disable unused inbound firewall rules
- Allow management access only from admin networks
- Restrict RDP with source filtering and MFA where available

## 3) Update and vulnerability hygiene

- Patch to latest cumulative update level
- Validate .NET and runtime dependencies
- Schedule routine monthly patch windows

## 4) Audit and monitoring

- Forward key Windows Event logs to central SIEM/log sink
- Enable alerts for logon failures and privilege changes
- Track CPU, memory, storage, and service health metrics

## 5) Backup and restore readiness

- Configure system state + data backup
- Test restore before production sign-off
- Record RPO/RTO targets per service

