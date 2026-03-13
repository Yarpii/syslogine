---
sidebar_position: 5
title: "Ubuntu SSH Configuration & Hardening"
sidebar_label: "SSH"
description: "Set up and harden OpenSSH on Ubuntu with keys, access policies, and operational security checks."
slug: /linux/ubuntu/ssh
---

# Ubuntu SSH Configuration & Hardening

SSH is the primary remote administration interface for most Ubuntu servers. Secure it early.

## 1. Confirm OpenSSH service

```bash
sudo systemctl status ssh
sudo systemctl enable --now ssh
```

## 2. Use key-based authentication

Generate key on workstation if needed:

```bash
ssh-keygen -t ed25519 -C "ops@community"
```

Copy key to server:

```bash
ssh-copy-id opsadmin@SERVER_IP
```

## 3. Harden server configuration

Edit `/etc/ssh/sshd_config`:

```text
PermitRootLogin no
PasswordAuthentication no
PubkeyAuthentication yes
X11Forwarding no
MaxAuthTries 3
ClientAliveInterval 300
ClientAliveCountMax 2
```

Validate and restart:

```bash
sudo sshd -t
sudo systemctl restart ssh
```

## 4. Restrict network exposure

Allow SSH through UFW (optionally from admin subnet only):

```bash
sudo ufw allow from 10.0.0.0/24 to any port 22 proto tcp
sudo ufw status
```

## 5. Audit access

```bash
sudo journalctl -u ssh --since "24 hours ago"
sudo tail -n 100 /var/log/auth.log
```

For brute-force protection, pair SSH with Fail2ban and key-only auth.
