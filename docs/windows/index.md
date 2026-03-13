---
title: "Windows Administration"
sidebar_label: "Overview"
sidebar_position: 1
description: "Windows documentation split into dedicated Desktop and Server administration tracks, with separate learning paths and operations guidance."
keywords:
  - windows administration
  - windows desktop
  - windows server
  - powershell
  - sysadmin
tags:
  - windows
  - system-administration
slug: /windows
---

# Windows Administration

You were absolutely right: **desktop and server are not the same discipline**.

This section is now organized around two clear tracks so readers can choose the correct path immediately.

## Choose your track

<div className="row">
  <div className="col col--6">
    <div className="card">
      <div className="card__header">
        <h3>🖥️ Windows Desktop Track</h3>
      </div>
      <div className="card__body">
        <p>For endpoint and workstation administration (Windows 10/11).</p>
        <ul>
          <li>User devices, policies, and local troubleshooting</li>
          <li>Endpoint patching, security, and software standards</li>
          <li>Enterprise desktop deployment and management</li>
        </ul>
      </div>
      <div className="card__footer">
        <a href="/docs/windows/desktop/getting-started" className="button button--primary button--block">Start Desktop Guides</a>
      </div>
    </div>
  </div>

  <div className="col col--6">
    <div className="card">
      <div className="card__header">
        <h3>🏢 Windows Server Track</h3>
      </div>
      <div className="card__body">
        <p>For infrastructure and production service administration.</p>
        <ul>
          <li>Identity, network, and server role operations</li>
          <li>Hardening, patch management, and backup planning</li>
          <li>Version-specific server workflows (2019/2022/2025)</li>
        </ul>
      </div>
      <div className="card__footer">
        <a href="/docs/windows/server/getting-started" className="button button--primary button--block">Start Server Guides</a>
      </div>
    </div>
  </div>
</div>

## Desktop vs Server: quick difference

| Area | Desktop | Server |
|---|---|---|
| Primary goal | End-user productivity | Service availability and reliability |
| Typical systems | Laptops/workstations | Datacenter VMs/physical servers |
| Management focus | User experience, app compatibility | Uptime, roles, capacity, security |
| Change cadence | Frequent endpoint updates | Controlled maintenance windows |

## Current guide entry points

### Desktop

- [Desktop overview](/docs/windows/desktop)
- [Desktop getting started](/docs/windows/desktop/getting-started)
- [Windows 10](/docs/windows/desktop/windows-10)
- [Windows 11](/docs/windows/desktop/windows-11)
- [Enterprise desktop](/docs/windows/desktop/enterprise)

### Server

- [Server overview](/docs/windows/server)
- [Server getting started](/docs/windows/server/getting-started)
- [Windows Server 2019](/docs/windows/server/2019)
- [Windows Server 2022](/docs/windows/server/2022)
- [Windows Server 2025](/docs/windows/server/2025)

### Shared skills

- [PowerShell guides](/docs/windows/powershell)
- [Windows IoT guides](/docs/windows/iot)

---

If you want, I can next add two dedicated learning paths:

1. **Desktop Administrator Path (L1 → L2)**
2. **Windows Server Administrator Path (Core → Advanced)**
