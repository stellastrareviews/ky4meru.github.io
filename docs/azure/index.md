---
layout: default
title: Azure
nav_order: 4
has_children: true
permalink: /azure/
has_toc: false
---

# Azure
{: .no_toc }

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

## Before to start

Install [AADInternals](https://github.com/Gerenios/AADInternals) as it provides useful cmdlets to interact with Azure.

```powershell
Import-Module AADInternals
```

## Recon

First, check if the Azure domain exists.

```bash
wget https://login.microsoftonline.com/$DomainName/.well-known/openid-configuration

# This one does not validate the username exists, only the domain.
wget https://login.microsoftonline.com/getuserrealm.srf?login=$Username@$DomainName

# Using AADInternals.
Get-AADIntTenantID -Domain $DomainName
```

Also, validate if a user exists like so.

```powershell
# Returns 0 or 6 if the username exists.
Invoke-RestMethod -Uri "https://login.microsoftonline.com/common/GetCredentialType" -ContentType "application/json" -Method POST -Body (@{"username"="$Email"; "isOtherIdpSupported" =  $true}|ConvertTo-Json) | Select -ExpandProperty IfExistsResult

# With AADInternals.
Get-AADIntLoginInformation -UserName $Email
```

## Without an account

* [Password Spraying](/azure/spraying/)
* [Subdomains Enumeration](/azure/subdomains/)
* [Synchronization Credentials Extraction](/azure/sync/)
* [User Spraying](/azure/userspraying/)

## Privilege escalation

Once you are authenticated on an Azure tenant, start by [enumerating it](/azure/enumeration/) before going through following attacks.

* [Applications Rights Abuse](/azure/apps/)

## Useful links

- [MSPortals.io](https://msportals.io/) to get the list of Microsoft administrator sites, training, and licensing resources.