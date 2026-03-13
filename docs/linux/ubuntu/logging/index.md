---
sidebar_position: 1
title: "Ubuntu Logging Guide for Community Servers"
sidebar_label: "Community Logging Guide"
description: "A practical Ubuntu logging guide for community projects: rsyslog basics, journal usage, log rotation, and central forwarding."
keywords:
  - ubuntu logging
  - rsyslog
  - journald
  - logrotate
  - sysadmin guide
slug: /linux/ubuntu/logging
---

# Ubuntu Logging Guide for Community Servers

This guide helps community maintainers set up reliable logging on Ubuntu with tools already included in most server installs.

## What you'll set up

- `systemd-journald` for service-level logs
- `rsyslog` for classic syslog handling
- `logrotate` to prevent disks filling up
- Optional remote forwarding to a central log server

## 1) Verify logging components

```bash
systemctl status systemd-journald
systemctl status rsyslog
```

If `rsyslog` is missing:

```bash
sudo apt update
sudo apt install -y rsyslog
sudo systemctl enable --now rsyslog
```

## 2) Read logs quickly (day-to-day operations)

### Journal (systemd services)

```bash
journalctl -u ssh --since "1 hour ago"
journalctl -p warning -b
journalctl -f
```

### Syslog files

```bash
sudo tail -n 100 /var/log/syslog
sudo tail -n 100 /var/log/auth.log
```

Use these two sources together when troubleshooting authentication, service failures, and boot-time issues.

## 3) Keep logs from consuming disk

Check current rotation policy:

```bash
sudo cat /etc/logrotate.conf
sudo ls /etc/logrotate.d/
```

Create a simple policy for a custom app log:

```bash
sudo tee /etc/logrotate.d/community-app >/dev/null <<'CONF'
/var/log/community-app/*.log {
  daily
  rotate 14
  compress
  delaycompress
  missingok
  notifempty
  create 0640 root adm
}
CONF
```

Test safely:

```bash
sudo logrotate -d /etc/logrotate.conf
```

## 4) (Optional) Forward logs to a central server

Add a forwarding rule:

```bash
sudo tee /etc/rsyslog.d/60-forwarding.conf >/dev/null <<'CONF'
*.* @@10.0.0.10:514
CONF
```

- `@@` uses TCP (recommended)
- `@` uses UDP

Then restart rsyslog:

```bash
sudo systemctl restart rsyslog
```

## 5) Basic hardening checklist

- Restrict log file permissions to admins and service accounts.
- Use TLS for remote logging when crossing untrusted networks.
- Keep clock sync enabled (`systemd-timesyncd` or NTP) for accurate incident timelines.
- Review `/var/log/auth.log` and sudo events regularly.

## Troubleshooting quick wins

- No new logs? Check disk space with `df -h` and service status with `systemctl status rsyslog`.
- Missing service logs? Verify unit name and run `journalctl -u <service> -xe`.
- Forwarding not working? Confirm route/firewall and test listener on destination (`ss -lntup | grep 514`).

---

With this setup, small community servers gain production-style visibility with minimal overhead.
