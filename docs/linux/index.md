---
layout: default
title: Linux
nav_order: 3
has_children: true
permalink: /linux/
---

# Linux
{: .no_toc }

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

## Without an account

First, you should perform a [Network Recon](/network/) of your target to identify quick wins or running services you could exploit. Once identified, refer to [Web Attacks](/webapps/) to attempt remote code execution that could give you an initial foothold on the target.

## Privilege escalation

Once you get a low privileged access on a Linux target, you can perform following attacks to escalate your privileges. You should perform [Linux Enumeration](/linux/enumeration/) to get as much information as possible before starting to exploit.

- [Cron Jobs Abuse](/linux/cron/)
- [Kernel Module Abuse](/linux/kernel/)
- [Passwd Abuse](/linux/passwd/)
- [Setuid Capabilities](/linux/setuid/)
- [Sudo Abuse](/linux/sudo)