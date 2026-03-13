---
sidebar_position: 3
title: "Ubuntu Initial Setup Guide"
sidebar_label: "Initial Setup"
description: "Post-install Ubuntu Server baseline: updates, users, SSH keys, firewall, time sync, and essential tooling."
slug: /linux/ubuntu/initial-setup
---

# Ubuntu Initial Setup Guide

Use this checklist immediately after installing Ubuntu Server.

## 1. Update packages

```bash
sudo apt update
sudo apt full-upgrade -y
sudo apt autoremove -y
```

## 2. Create an operations user

```bash
sudo adduser opsadmin
sudo usermod -aG sudo opsadmin
id opsadmin
```

## 3. Configure SSH key authentication

From your workstation:

```bash
ssh-copy-id opsadmin@SERVER_IP
```

On server (`/etc/ssh/sshd_config`), enforce best-practice defaults:

- `PermitRootLogin no`
- `PasswordAuthentication no` (after key login is verified)

Apply changes:

```bash
sudo systemctl restart ssh
```

## 4. Enable firewall (UFW)

```bash
sudo ufw default deny incoming
sudo ufw default allow outgoing
sudo ufw allow OpenSSH
sudo ufw enable
sudo ufw status verbose
```

## 5. Verify time synchronization

```bash
timedatectl status
systemctl status systemd-timesyncd
```

## 6. Install baseline admin tools

```bash
sudo apt install -y curl wget htop jq git vim tmux unzip
```

## 7. Record baseline state

```bash
uname -a
lsb_release -a
df -h
free -h
```

Store these outputs in your ops wiki/ticket for future troubleshooting.

## Next step

Proceed to [Package Management](/docs/linux/ubuntu/package-management) for safe update and repository workflows.
