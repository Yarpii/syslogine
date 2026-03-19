---
sidebar_position: 4
title: "Sudo & Privileges | AlmaLinux"
sidebar_label: "Sudo & Privileges"
description: "Configure sudo policies, implement privilege escalation controls, and manage administrative access securely on AlmaLinux."
keywords:
  - "almalinux sudo"
  - "almalinux sudoers"
  - "almalinux privilege escalation"
  - "almalinux visudo"
  - "rhel sudo configuration"
tags:
  - almalinux
  - user-management
  - system-administration
  - security
slug: /linux/almalinux/administration/user-management/sudo-privileges
---

# Sudo & Privileges

`sudo` allows regular users to run commands with elevated privileges without sharing the root password. On AlmaLinux, sudo is configured through `/etc/sudoers` and files in `/etc/sudoers.d/`. Done well, it gives administrators fine-grained control over who can do what — and leaves an audit trail of every privileged action.

## How sudo works on AlmaLinux

AlmaLinux follows the RHEL convention: members of the `wheel` group have full sudo access by default. This is configured in `/etc/sudoers`:

```bash
# View the relevant line
sudo grep wheel /etc/sudoers
# %wheel  ALL=(ALL)  ALL
```

This means the simplest way to give a user full sudo access is to add them to `wheel`.

## Give a user full sudo access

```bash
sudo usermod --append --groups wheel jdoe
```

The user needs to log out and back in for the change to take effect. Verify:

```bash
# Switch to the user and test
su - jdoe
sudo whoami
# root
```

## Edit sudoers safely with visudo

Never edit `/etc/sudoers` directly with a text editor. Always use `visudo`, which validates the syntax before saving. A broken sudoers file can lock you out of sudo entirely.

```bash
sudo visudo
```

To use a different editor:

```bash
sudo EDITOR=nano visudo
```

## Sudoers syntax

The basic format of a sudoers rule is:

```
WHO  WHERE=(AS_WHOM)  WHAT
```

| Field | Description |
|-------|-------------|
| `WHO` | User or `%group` |
| `WHERE` | Hostname or `ALL` |
| `AS_WHOM` | User to run as, usually `ALL` or `root` |
| `WHAT` | Command(s) or `ALL` |

Examples:

```bash
# Full sudo access for a user
jdoe  ALL=(ALL)  ALL

# Full sudo access for the wheel group
%wheel  ALL=(ALL)  ALL

# Run a specific command only
jdoe  ALL=(ALL)  /usr/bin/systemctl restart nginx

# Run specific commands without a password
jdoe  ALL=(ALL)  NOPASSWD: /usr/bin/systemctl status *, /usr/bin/journalctl
```

## Use /etc/sudoers.d/ for clean management

Rather than editing the main sudoers file, place custom rules in separate files under `/etc/sudoers.d/`. This keeps things organized and easier to manage.

```bash
# Create a new sudoers rule file
sudo visudo -f /etc/sudoers.d/developers
```

Example content:

```bash
# /etc/sudoers.d/developers
# Developers can restart application services
%developers  ALL=(ALL)  /usr/bin/systemctl restart app*, /usr/bin/systemctl status app*
```

Set the correct permissions — sudoers files must not be world-writable:

```bash
sudo chmod 440 /etc/sudoers.d/developers
```

:::warning
Files in `/etc/sudoers.d/` must have permissions `440` or `400`. If permissions are too open, sudo will ignore the file and log a warning.
:::

## Grant sudo access to a group

```bash
sudo visudo -f /etc/sudoers.d/ops
```

```bash
# /etc/sudoers.d/ops
# Ops team has full sudo access
%ops  ALL=(ALL)  ALL
```

## Restrict sudo to specific commands

Limiting users to only the commands they need follows the principle of least privilege.

```bash
sudo visudo -f /etc/sudoers.d/webadmin
```

```bash
# /etc/sudoers.d/webadmin
# Web admins can manage nginx only
%webadmin  ALL=(ALL)  /usr/bin/systemctl start nginx, \
                      /usr/bin/systemctl stop nginx, \
                      /usr/bin/systemctl restart nginx, \
                      /usr/bin/systemctl status nginx, \
                      /usr/bin/nginx -t
```

## Allow commands without a password prompt

Use `NOPASSWD` carefully — it removes the friction that protects against accidental or unauthorized privilege use. Reserve it for automation and service accounts, not interactive admin users.

```bash
# /etc/sudoers.d/monitoring
# Monitoring agent can read logs without a password
monitoragent  ALL=(ALL)  NOPASSWD: /usr/bin/journalctl, \
                                   /usr/bin/systemctl status *
```

## Use command aliases for cleaner rules

Aliases group related commands together, making rules easier to read and maintain.

```bash
sudo visudo
```

```bash
# Define aliases at the top of the file or in a sudoers.d file
Cmnd_Alias  WEBSERVICES = /usr/bin/systemctl start nginx, \
                          /usr/bin/systemctl stop nginx, \
                          /usr/bin/systemctl restart nginx

Cmnd_Alias  DBSERVICES  = /usr/bin/systemctl start postgresql, \
                          /usr/bin/systemctl stop postgresql, \
                          /usr/bin/systemctl restart postgresql

# Assign aliases to groups
%webadmin  ALL=(ALL)  WEBSERVICES
%dbadmin   ALL=(ALL)  DBSERVICES
```

## Run a command as a different user

sudo can run commands as users other than root. This is useful for application deployments where commands should run as the app user.

```bash
# Run a command as the 'appuser' account
sudo -u appuser /opt/app/deploy.sh

# Open a shell as appuser
sudo -u appuser -s
```

In sudoers:

```bash
# Allow the deploy group to run deploy scripts as appuser
%deploy  ALL=(appuser)  /opt/app/deploy.sh, /opt/app/rollback.sh
```

## Check what sudo permissions a user has

```bash
# Check your own permissions
sudo -l

# Check another user's permissions (as root)
sudo -l -U jdoe
```

Example output:

```
User jdoe may run the following commands on this host:
    (ALL) /usr/bin/systemctl restart nginx
    (ALL) NOPASSWD: /usr/bin/systemctl status *
```

## Sudo timeout and re-authentication

By default, sudo caches credentials for 5 minutes. After that, it prompts for a password again. You can adjust this per rule or globally.

```bash
sudo visudo
```

```bash
# Increase the timeout to 15 minutes globally
Defaults  timestamp_timeout=15

# Disable caching entirely — always prompt for a password
Defaults  timestamp_timeout=0

# Require password per terminal session, not per user
Defaults  timestamp_type=tty
```

## Require a password even for wheel members

By default on some configurations, wheel members can run sudo without re-entering their password. To enforce password prompts:

```bash
sudo visudo
```

Make sure this line is active (not commented out):

```bash
%wheel  ALL=(ALL)  ALL
```

And this line is commented out:

```bash
# %wheel  ALL=(ALL)  NOPASSWD: ALL
```

## Audit sudo usage

Every sudo command is logged by default. On AlmaLinux, check the logs with:

```bash
# View sudo activity in the system journal
sudo journalctl | grep sudo

# View recent sudo events
sudo journalctl -u sudo --since today

# Grep the auth log
sudo grep sudo /var/log/secure
```

Example log entry:

```
Mar 19 10:42:17 hostname sudo: jdoe : TTY=pts/0 ; PWD=/home/jdoe ; USER=root ; COMMAND=/usr/bin/systemctl restart nginx
```

## Revoke sudo access

```bash
# Remove from wheel group
sudo gpasswd --delete jdoe wheel

# Or remove a specific sudoers.d file
sudo rm /etc/sudoers.d/jdoe

# Verify permissions are gone
sudo -l -U jdoe
```

## Validate a sudoers file

Before applying changes, validate a sudoers file for syntax errors:

```bash
sudo visudo --check --file /etc/sudoers.d/developers
```

If the file is valid, visudo exits silently. If there is an error, it prints the line number and description.

## Common mistakes

### Editing /etc/sudoers directly

If you introduce a syntax error, sudo stops working entirely. Always use `visudo`.

```bash
# WRONG
sudo nano /etc/sudoers

# CORRECT
sudo visudo
```

### Using NOPASSWD for interactive admin accounts

This removes an important layer of protection. Reserve `NOPASSWD` for automation, scripts, and service accounts.

### Overly broad command rules

Allowing `ALL` commands for a user defeats the purpose of restricting access. Be as specific as possible.

```bash
# Too broad
jdoe  ALL=(ALL)  /usr/bin/systemctl

# Better — only allow specific service actions
jdoe  ALL=(ALL)  /usr/bin/systemctl restart nginx, /usr/bin/systemctl status nginx
```

### Wrong permissions on sudoers.d files

Files with permissions wider than `440` are silently ignored by sudo.

```bash
sudo chmod 440 /etc/sudoers.d/developers
```

### Not auditing sudo usage

Sudo logs every command. Regularly reviewing those logs helps catch misuse early.

```bash
sudo grep sudo /var/log/secure | tail -50
```

## Summary

sudo is one of the most important tools for managing administrative access on AlmaLinux. Used well, it gives the right people access to the right commands — with a full audit trail.

Key points:

- add users to the `wheel` group for full sudo access
- always use `visudo` to edit sudoers files safely
- place custom rules in `/etc/sudoers.d/` for clean management
- restrict commands to exactly what each user or group needs
- use `NOPASSWD` only for automation and service accounts
- regularly audit sudo usage in `/var/log/secure` or the journal
- validate sudoers files with `visudo --check` before applying

:::tip Related pages
- [Creating Users](./creating-users)
- [Group Management](./group-management)
- [Authentication Systems](./authentication-systems)
- [User Auditing](./user-auditing)
:::