---
layout: default
title: Active Directory
nav_order: 2
has_children: true
permalink: /ad/
has_toc: false
---

# Active Directory
{: .no_toc }

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

{: .important }
> This section assumes that you have **at least a network access** to the Active Directory domain.

## Recon

Before starting to play with following exploits, it is a good idea to recover information about the domain you are going to attack as an unauthenticated user.

- [First Lookup](/ad/lookup/)

## Without an account

First thing you want to achive to compromise a domain is to get an initial foothold. To do so, following attacks can be used to gain a domain joined account, either user or computer one. [Web attacks](/webapps/) can also be a good entrypoint on an Active Directory domain.

- [Bruteforce](/ad/bruteforce/)
- [Email Password Spraying](/ad/email/)
- [NTLM Relay](/ad/ntlmrelay/)
- [Password Spraying](/ad/spraying)
- [Printer Abuse](/ad/printer/)
- [Zerologon [CVE-2020-1472]](/ad/zerologon/)

You also should take a look at [Windows Initial Foothold](/windows/#without-an-account) methods.

## Privilege escalation

Once you get either a low privileged domain account or an access to a domain joined computer, you can perform following attacks to escalate your privileges locally on domain computers or on the domain itself. You should perform [Active Directory Enumeration](/ad/enumeration/) to get as much information as possible before starting to exploit.

- [AS-REP Roasting](/ad/asreproasting/)
- [Certificate Services](/ad/certs/)
- [Constrained Delegation](/ad/constrained/)
- [DCSync Attack](/ad/dcsync/)
- [Diamond Tickets](/ad/diamond/)
- [Golden Tickets](/ad/golden/)
- [Kerberoasting](/ad/kerberoasting/)
- [LAPS Abuse](/ad/laps/)
- [NoPac [CVE-2021-42278] & [CVE-2021-42287]](/ad/nopac/)
- [PetitPotam [CVE-2022-26925]](/ad/petitpotam/)
- [Pre Windows 2000 Compatibility Abuse](/ad/compatibility/)
- [PrintNightmare [CVE-2021-1675]](/ad/printnightmare/)
- [Role-Based Constrained Delegation](/ad/rbconstrained/)
- [SCCM Abuse](/ad/sccm/)
- [S4U2Self Abuse](/ad/s4u2self/)
- [Shadow Credentials](/ad/shadow/)
- [SYSVOL Enumeration](/ad/sysvol/)
- [Unconstrained Delegation](ad/unconstrained/)

You also should take a look at [Windows Privilege Escalation](/windows/#privilege-escalation) methods.

## Lateralization

- [Net-NTLMv2 Relay](/ad/netntlmv2relay/)
- [Overpass the Hash](/ad/overpassthehash/)
- [Pass the Ticket](/ad/passtheticket/)
- [Service Name Abuse](/ad/servicename/)
- [Silver Tickets](/ad/silver/)
- [Trust Relationship Abuse](/ad/trust/)

You also should take a look at [Windows Lateralization](/windows/#lateralization) methods.

## Useful links

- [ODC Mind Map](https://orange-cyberdefense.github.io/ocd-mindmaps/img/pentest_ad_dark_2023_02.svg) for the Orange Cyberdefense Active Directory mind map.