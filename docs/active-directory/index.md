---
layout: default
title: Active Directory
nav_order: 2
has_children: true
permalink: /ad/
---

# Windows
{: .no_toc }

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

{: .important }
> This section assumes that you have **at least a network access** to the Active Directory domain.

## TODO

- First Lookup
- NetNTLMv2 Relay: encoding PS one liner
- NoPac [CVE-2021-42278] & [CVE-2021-42287]
- Passwords expired
- Port forwarding to lateralize (which chisel or powershell)
- Privileges Abuse (GenericAll, AllExtendedRights, etc.)
- PSExec
- SeImpersonatePrivilege Abuse
- Shares Enumeration
- SNMP attacks on Windows

## Recon

Before starting to play with following exploits, it is a good idea to recover information about the domain you are going to attack as an unauthenticated user.

- [First Lookup](/ad/lookup/)

## Without an account

First thing you want to achive to compromise a domain is to get an initial foothold. To do so, following attacks can be used to gain a domain joined account, either user or computer one. [Web attacks](../web/README.md) can also be a good entrypoint on an Active Directory domain.

- [BlueKeep [CVE-2019-0708]](/windows/bluekeep/)
- [Bruteforce](/ad/bruteforce/)
- [EternalBlue [MS17-010]](/windows/eternalblue/)
- [NTLM Relay](/ad/ntlmrelay/)
- [Password Spraying](/ad/spraying)
- [Printer Abuse](/ad/printer/)
- [Zerologon [CVE-2020-1472]](/ad/zerologon/)

## Privilege escalation

Once you get either a low privileged domain account or an access to a domain joined computer, you can perform following attacks to escalate your privileges locally on domain computers or on the domain itself. You should perform [Active Directory Enumeration](/ad/enumeration/) to get as much information as possible before starting to exploit.

- [AS-REP Roasting](/ad/asreproasting/)
- [Cached Credentials Extraction](/ad/credentials/)
- [DCSync Attack](/ad/dcsync/)
- [Kerberoasting](/ad/kerberoasting/)
- [NoPac [CVE-2021-42278] & [CVE-2021-42287]](/ad/nopac/)
- [PetitPotam [CVE-2022-26925]](/ad/petitpotam/)
- [Pre Windows 2000 Compatibility Abuse](/ad/compatibility/)
- [PrintNightmare [CVE-2021-1675]](/ad/printnightmare/)
- [SeImpersonatePrivilege Abuse](/windows/seimpersonateprivilege/)
- [SYSVOL Enumeration](/ad/sysvol/)

## Lateralization

- [Net-NTLMv2 Relay](/ad/netntlmv2relay/)