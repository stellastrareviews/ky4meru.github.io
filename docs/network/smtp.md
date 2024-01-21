---
layout: default
title: SMTP Enumeration
parent: Network
nav_order: 1
permalink: /network/smtp/
---

# SMTP Enumeration
{: .no_toc }

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

## Vulnerability

TODO: Describe the vulnerability here.

## Prerequisites

* Network route to the network you want to scan.

## Exploit

```bash
# Using netcat
nc -nv <TARGET> 25

# Using Test-NetConnection on Windows PowerShell
Test-NetConnection -Port 25 <TARGET>

# Using telnet
telnet <TARGET> 25
```

## Useful links

* TODO: List links here.

## Recommendations

- [ ] TODO: List recommendations here.