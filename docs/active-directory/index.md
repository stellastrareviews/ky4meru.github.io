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

- [First Lookup](./firstlookup.md)

## Without an account

First thing you want to achive to compromise a domain is to get an initial foothold. To do so, following attacks can be used to gain a domain joined account, either user or computer one. [Web attacks](../web/README.md) can also be a good entrypoint on an Active Directory domain.

- [BlueKeep [CVE-2019-0708]](/windows/bluekeep/)
- [Bruteforce](./bruteforce.md)
- [EternalBlue [MS17-010]](../windows/eternalblue.md)
- [NTLM Relay](./ntlmrelay.md)
- [Password Spraying](./passwordspraying.md)
- [Printer Abuse](./printer.md)
- [Zerologon [CVE-2020-1472]](/ad/zerologon/)

## Privilege escalation

Once you get either a low privileged domain account or an access to a domain joined computer, you can perform following attacks to escalate your privileges locally on domain computers or on the domain itself. You should perform [Active Directory Enumeration](./enumeration.md) to get as much information as possible before starting to exploit.

- [AS-REP Roasting](./asreproasting.md)
- [Cached Credentials Extraction](./credentials.md)
- [DCSync Attack](./dcsync.md)
- [Kerberoasting](./kerberoasting.md)
- [NoPac [CVE-2021-42278] & [CVE-2021-42287]](./nopac.md)
- [PetitPotam [CVE-2022-26925]](./petitpotam.md)
- [Pre Windows 2000 Compatibility Abuse](./compatibility.md)
- [PrintNightmare [CVE-2021-1675]](./printnightmare.md)
- [SeImpersonatePrivilege Abuse](../windows/seimpersonateprivilege.md)
- [SYSVOL Enumeration](./sysvol.md)

## Lateralization

- [Net-NTLMv2 Relay](./netntlmv2relay.md)