# Syslogine

Syslogine is a documentation platform for practical system administration.
It provides structured, step-by-step guides that help learners and working
admins deploy, secure, troubleshoot, and automate real infrastructure.

## What Syslogine covers

- Linux administration tutorials across multiple distributions
- Windows Server operations and enterprise management workflows
- Networking fundamentals and hands-on configuration guides
- Security hardening, monitoring, and recovery practices
- Cloud, virtualization, and automation-oriented operations topics

## Who it is for

Syslogine is designed for:

- Beginners building foundational sysadmin skills
- IT professionals transitioning into infrastructure roles
- Experienced administrators looking for reference runbooks
- Teams standardizing operational procedures

## How the content is organized

The docs are organized by platform and topic area so readers can either:

- Follow a guided learning path, or
- Jump directly to task-focused implementation pages

Each section aims to stay practical: clear goals, commands, configuration
examples, and actionable procedures.

## Start reading

- Main introduction: `docs/intro.md`
- Linux guides: `docs/linux/`
- Windows guides: `docs/windows/`
- Networking guides: `docs/network/`
- Security guides: `docs/security/`

## Local development

```bash
npm install
npm run start
```

Build static docs:

```bash
npm run build
```
