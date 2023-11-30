---
layout: default
title: Windows
nav_order: 5
has_children: true
permalink: /windows/
---

# Windows
{: .no_toc }

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

## TODO

- SNMP Enumeration

## Without an account

First, you should perform a [Network Recon](../network/README.md) of your target to identify quick wins or running services you could exploit. Once identified, refer to [Web Attacks](../web/README.md) to attempt remote code execution that could give you an initial foothold on the target or try following exploits.

- [BlueKeep [CVE-2019-0708]](./bluekeep.md)
- [EternalBlue [MS17-010]](./eternalblue.md)

## Privilege escalation

Once you get a low privileged access on a Windows target, you can perform following attacks to escalate your privileges locally. You should perform [Windows Enumeration](./enumeration.md) to get as much information as possible before starting to exploit.

- [SeImpersonatePrivilege Abuse](./seimpersonateprivilege.md)

More privilege escalation exploits are be possible if your target is joined to an [Active Directory](../active-directory/README.md#privilege-escalation) since you can use your target's machine account to authenticate on the domain.