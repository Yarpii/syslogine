---
sidebar_position: 2
title: "Ubuntu Server Installation Tutorial"
sidebar_label: "Installation"
description: "Step-by-step Ubuntu Server installation tutorial for community homelabs and production-ready starter deployments."
slug: /linux/ubuntu/installation
---

# Ubuntu Server Installation Tutorial

This tutorial walks through a clean Ubuntu Server installation with sensible defaults for community projects, labs, and small production environments.

## Prerequisites

- Ubuntu Server ISO (LTS recommended)
- 2+ vCPU, 4+ GB RAM, 25+ GB disk
- Stable network connection
- Access to console (physical, IPMI, or VM console)

## 1. Boot and start installer

1. Boot from the Ubuntu Server ISO.
2. Choose language and keyboard layout.
3. Select **Install Ubuntu Server**.

## 2. Configure networking

- Use DHCP for quick setup, or
- Set static IP if this host will run infrastructure services.

Record:

- Hostname
- IP address
- Gateway
- DNS servers

## 3. Storage layout

Recommended baseline:

- `/` (root): majority of disk
- `swap`: 2–4 GB (or use swapfile later)
- `/var` separate partition if expecting heavy logs/data

For encrypted deployments, enable LUKS at install time.

## 4. Create admin user

- Create one admin account.
- Use a strong password/passphrase.
- Disable direct root login by default.

## 5. Install OpenSSH server

Select **OpenSSH server** during installer package selection so remote management is available immediately after first boot.

## 6. First boot checks

After reboot, verify host health:

```bash
hostnamectl
ip a
systemctl --failed
```

Then patch the system:

```bash
sudo apt update
sudo apt full-upgrade -y
sudo reboot
```

## Next step

Continue with [Initial Setup](/docs/linux/ubuntu/initial-setup) to harden and baseline the server.
