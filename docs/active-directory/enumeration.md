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

{: .important }
> At this point, you might be authenticated on a domain joined computer - *via RDP, WinRM or whatever* so **do not forget** to [enumerate Windows](/windows/enumeration/) as well.

## Vulnerability

Active Directory enumeration is not a vulnerability by itself. Actually, it is more like a feature because Active Directory is built this way. Once you are authenticated on a domain, you are able to list all Active Directory objects and their relations. It is a very useful way to discover how to escalate you privileges by better understanding how the domain is organized.

## Prerequisites

- Low privileged domain account.

## Automated tools

### BloodHound

The best tool to enumerate and get graphical insights of an Active Directory domain is [BloodHound](https://github.com/BloodHoundAD/BloodHound) and associated ingestors like [BloodHound.py](/ad/enumeration/#BloodHound.py) or [SharpHound](/ad/enumeration/#SharpHound).

First, you will need to extract all objects of the domain. These ingestors will do it and will generate multiple files containing domain's objects. You can now import these files in [BloodHound](https://github.com/BloodHoundAD/BloodHound) to visualize domain's organization and find attack paths.

### BloodHound.py


With [BloodHound.py](https://github.com/dirkjanm/BloodHound.py).

```bash
# From Kali, ingest domain objects with BloodHound.py.
bloodhound-python -d $domain -u $username -p $password -c All
```

### SharpHound

With [SharpHound](https://github.com/BloodHoundAD/SharpHound).

```powershell
# From a domain joined Windows computer.
.\SharpHound.exe -c All

# From a Windows computer which is not joined to the domain.
.\SharpHound.exe -d $domain --ldapusername $username --ldappassword $password -c All
```

{: .important }
> You might be connected on a domain joined computer with a local account. In such a case, you won't be able to start `SharpHound.exe` from a PowerShell started with this local account.
> To bypass this, you must run `SharpHound.exe` from a PowerShell started as `NT AUTHORITY\SYSTEM` so `SharpHound.exe` will use domain computer credentials to authenticate on the Active Directory domain.
> To do so, you can use [PSExec](https://learn.microsoft.com/en-us/sysinternals/downloads/psexec) by running `Psexec.exe -i -s C:\WINDOWS\system32\WindowsPowerShell\v1.0\powershell.exe`.

### PingCastle

[PingCastle](https://www.pingcastle.com/) is also a very good candidate to enumerate the Active Directory domain. It will provide risks insights, highlighting misconfigurations and recommendations to apply.

```bash
# From the domain controller itself.
.\PingCastle.exe --healthcheck

# From a distant computer.
.\PingCastle.exe --healthcheck --server $domain --user $username --password $password
```

### PowerView and SharpView

PowerView from [PowerSploit](https://github.com/PowerShellMafia/PowerSploit/) framework is a PowerShell module that provides many buitins to enumerate an Active Directory domain.

```powershell
powershell -ep bypass
Import=Module ./PowerView.ps1
Get-DomainController -Domain $domain
```

[SharpView](https://github.com/tevora-threat/SharpView) is a .NET port for PowerView, with the same features.

```powershell
.\SharpView.exe Get-DomainController -Domain $domain
.\SharView.exe $command $options
```

### ADSearch

[ADSearch](https://github.com/tomcarver16/ADSearch) allows to perform custom LDAP queries.

```powershell
.\ADSearch.exe --search "$ldap_filter"
```

### ADRecon

[ADRecon](https://github.com/adrecon/ADRecon) will do a quick enumeration of basic information about an Active Directory domain.

```powershell
# From a domain joined computer.
.\ADRecon.ps1

# From a workgroup computer.
.\ADRecon.ps1 -DomainController $dc_ip -Credential $domain\$username
```

## Manual enumeration 

This section references who to manually enumerate a Windows host using one or more of the following methods (in the order):
* Using Windows builtins that can be invoked via `cmd.exe`.
* Using native PowerShell.
* Using [ActiveDirectory](https://learn.microsoft.com/en-us/powershell/module/activedirectory/?view=windowsserver2022-ps) PowerShell module.
* Using [ADSearch](https://github.com/tomcarver16/ADSearch).
* Using - *your best friend* - [PowerView](https://github.com/PowerShellMafia/PowerSploit/).

### AppLocker

If AppLocker is enabled, take a look at some ways to [bypass](/ad/applocker/) it.

```powershell
reg query "HKLM\Software\Policies\Microsoft\Windows\SrpV2"
Get-ChildItem "HKLM:Software\Policies\Microsoft\Windows\SrpV2"
Get-AppLockerPolicy -Effective -XML
Get-AppLockerFileInformation | Format-List
Get-DomainGPO -Domain dev-studio.com | ? { $_.DisplayName -like "*AppLocker*" } | select displayname, gpcfilesyspath
```

### Custom LDAP search

```powershell
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
```

### GPO

If you can create or modify GPO, refer to [GPO Abuse](/ad/gpo/) attack to leverage the situation.

```powershell
# List GPOs and lookup on principals that can CreateChild or modify them.
Get-DomainGPO | Get-DomainObjectAcl -ResolveGUIDs | ? { $_.ActiveDirectoryRights -match "CreateChild|WriteProperty" }

# Get domain objects that can create new GPO.
Get-DomainObjectAcl -Identity "CN=Policies,CN=System,DC=$domain,DC=$com" -ResolveGUIDs | ? { $_.ObjectAceType -Eq "Group-Policy-Container" -And $_.ActiveDirectoryRights -Contains "CreateChild" } | % { ConvertFrom-SID $_.SecurityIdentifier }

# Get domain objects that can link GPO to an OU.
Get-DomainOU | Get-DomainObjectAcl -ResolveGUIDs | ? { $_.ObjectAceType -eq "GP-Link" -and $_.ActiveDirectoryRights -match "WriteProperty" } | Select-Object -Property ObjectDN,ActiveDirectoryRights,ObjectAceType,SecurityIdentifier | Format-List
```

### LAPS

If LAPS is enabled, you could [abuse](/ad/laps/) its configuration to escalate your privileges.

```powershell
dir "C:\Program Files\LAPS\CSE\"
Get-DomainComputer | ? { $_."ms-Mcs-AdmPwdExpirationTime" -ne $null } | select dnsHostName
Get-DomainGPO | ? { $_.DisplayName -like "*laps*" } | select DisplayName, Name, GPCFileSysPath | Format-List
```

### SCCM

If following commands returns something, take a look at [SCCM Abuse](/ad/sccm/) attack.

```powershell
Get-WmiObject -Class SMS_Authority -Namespace root\CCM | select Name, CurrentManagementPoint | Format-List
```

### Shares

```powershell
Find-DomainShare -CheckShareAccess
Find-InterestingDomainShareFile -Include *.doc*, *.xls*, *.csv, *.ppt*
```

### Trust relationships

If you discover trust relationships, you could [abuse](/ad/trust/) them to lateralize on trusted and/or trusting domains.

```powershell
.\ADSearch.exe --search "(objectCategory=trustedDomain)" --domain $domain --attributes distinguishedName,name,flatName,trustDirection
Get-DomainTrust -Domain $domain
```

### Users

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
```

## Recommentations

- [ ] There is **no way to avoid Active Directory enumeration** for an authenticated user on the domain. The only recommendation here is to harden enough the domain to avoid privilege escalation, even if the attacker has an initial foothold.