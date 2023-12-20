---
layout: default
title: Active Directory Enumeration
parent: Active Directory
nav_order: 5
permalink: /ad/enumeration/
---

# Active Directory Enumeration
{: .no_toc }

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

## Vulnerability

Active Directory enumeration is not a vulnerability by itself. Actually, it is more like a feature because Active Directory is built this way. Once you are authenticated on a domain, you are able to list all Active Directory objects and their relations. It is a very useful way to discover how to escalate you privileges by better understanding how the domain is organized.

## Prerequisites

- Low privileged domain account.

## Automated tools

The best tool to enumerate and get graphical insights of an Active Directory domain is `BloodHound`.

First, you will need to extract all objects of the domain.

```bash
# From Kali.
bloodhound-python -d $domain -u $username -p $password -c All

# From a domain joined Windows computer.
.\SharpHound.exe -c All

# From a Windows computer which is not joined to the domain.
.\SharpHound.exe -d $domain --ldapusername $username --ldappassword $password -c All
```

This operation will generate multiple files containing domain's objects. You can now import these files in `BloodHound` to visualize domain's organization and find attack paths.

`PingCastle` is also a very good candidate to enumerate the Active Directory domain. It will provide risks insights, highlighting misconfigurations and recommendations to apply.

```bash
# From the domain controller itself
.\PingCastle.exe --healthcheck

# From a distant computer
.\PingCastle.exe --healthcheck --server $domain --user $username --password $password
```

## Manual enumeration 

```powershell
# Using Net.exe
net user /domain
net user
net user $username /domain
net group /domain
net localgroup
net group $group [/domain]
net accounts

# Using ActiveDirectory PowerShell module.
Get-ADGroupMember 'domain admins' | select samaccountname
Get-ADUser -Filter {PasswordExpired -eq $True} -Properties PasswordLastSet, PasswordExpired, PasswordNeverExpires | Sort-Object Name

# In a PowerShell file...
function LDAPSearch {
    param (
        [string]$LDAPQuery
    )

    $PDC = [System.DirectoryServices.ActiveDirectory.Domain]::GetCurrentDomain().PdcRoleOwner.Name
    $DistinguishedName = ([adsi]'').distinguishedName
    $DirectoryEntry = New-Object System.DirectoryServices.DirectoryEntry("LDAP://$PDC/$DistinguishedName")
    $DirectorySearcher = New-Object System.DirectoryServices.DirectorySearcher($DirectoryEntry, $LDAPQuery)
    return $DirectorySearcher.FindAll()
}

# ...then import and use it
powershep -ep bypass
Import-Module ./ldapsearch.ps1
LDAPSearch -LDAPQuery "$ldap_filter"

# Using PowerView (https://powersploit.readthedocs.io/en/latest/Recon/)
powershell -ep bypass
Import=Module ./PowerView.ps1
Get-NetDomain
Get-NetUser
Get-LocalUser
Get-LocalGroup
Get-NetUser | select cn,pwdlastset,lastlogon
Get-NetGroup | select cn
Get-NetComputer | select operatingsystem,dnshostname
Find-LocalAdminAccess
Get-NetSession -ComputerName $target -Verbose
setspn -L $username
Get-NetUser -SPN | select samaccountname,serviceprincipalname
Find-DomainShare
```

## Recommentations

- [ ] There is **no way to avoid Active Directory enumeration** for an authenticated user on the domain. The only recommendation here is to harden enough the domain to avoid privilege escalation, even if the attacker has an initial foothold.