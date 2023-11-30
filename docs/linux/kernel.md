---
layout: default
title: Kernel Module Abuse
parent: Linux
nav_order: 4
permalink: /linux/kernel/
---

# Kernel Module Abuse
{: .no_toc }

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

## Prerequisites

- Low privileged access to the Linux target

## Exploit

First, get information about your target system.

```bash
# Linux version
cat /etc/issue

# Kernel version
uname -r

# CPU architecture
arch
```

Once you have all prerequisites in mind, use `searchsploit`, or anything else, to find your exploit.