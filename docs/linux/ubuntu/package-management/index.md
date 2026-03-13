---
sidebar_position: 4
title: "Ubuntu Package Management (APT)"
sidebar_label: "Package Management"
description: "Practical APT workflows for installing, updating, troubleshooting, and safely maintaining Ubuntu servers."
slug: /linux/ubuntu/package-management
---

# Ubuntu Package Management (APT)

APT is the standard package manager for Ubuntu. This guide focuses on practical command patterns for routine operations.

## Core commands

```bash
sudo apt update
sudo apt upgrade
sudo apt full-upgrade
sudo apt install <package>
sudo apt remove <package>
sudo apt purge <package>
```

## Search and inspect packages

```bash
apt search nginx
apt show nginx
apt list --installed | head
```

## Safe patching workflow (recommended)

1. Refresh metadata:
   ```bash
   sudo apt update
   ```
2. Preview upgrades:
   ```bash
   apt list --upgradable
   ```
3. Apply updates:
   ```bash
   sudo apt upgrade -y
   ```
4. Reboot if kernel/core libraries changed:
   ```bash
   sudo reboot
   ```

## Repository hygiene

- Prefer official Ubuntu repositories.
- Add third-party repos only when required.
- Track every added source in change management docs.

List active sources:

```bash
grep -hE '^[^#]' /etc/apt/sources.list /etc/apt/sources.list.d/*.list 2>/dev/null
```

## Common troubleshooting

Fix interrupted dpkg state:

```bash
sudo dpkg --configure -a
```

Fix broken dependencies:

```bash
sudo apt --fix-broken install
```

Clean old cache/packages:

```bash
sudo apt autoremove -y
sudo apt clean
```
