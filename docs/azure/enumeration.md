---
layout: default
title: Microsoft Entra ID Enumeration
parent: Templates
nav_order: 1
permalink: /azure/enumeration/
---

# Microsoft Entra ID Enumeration
{: .no_toc }

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

## Vulnerability

TODO: Describe the vulnerability here.

## Prerequisites

* Being authenticated in the Azure tenant you want to enumerate.

## Automated tools

### AzureHound

[AzureHound](https://github.com/BloodHoundAD/AzureHound) is a [BloodHound](https://github.com/BloodHoundAD/BloodHound) ingestor for Microsoft Entra ID. It's written in Go, so it could be run from both Windows and Linux. 

```bash
# Enumerate tenant as an authenticated user and write the output in JSON format.
# The JSON output file can be imported in BloodHound.
azurehound list -u "$UserPrincipalName" -p "$Password" -t "$TenantID" -o "output.json"
```

### ROADrecon

Use [ROADrecon](https://github.com/dirkjanm/ROADtools) to extract Microsoft Entra ID objects as a database you can then browse through a tiny local web application.

```bash
# Authenticate with MFA if enabled.
roadrecon auth --device-code

# Retrieve all information in roadrecon.db.
roadrecon gather

# Start local web application on http://127.0.0.1:5000/ to browse gathered information.
roadrecon gui
```

## Manuel enumeration

This section references how to manually enumerate Azure using [AzureAD](https://www.powershellgallery.com/packages/AzureAD) PowerShell module. Before that, you need to authenticate on the tenant as shown below.

```powershell
Install-Module AzureAD
Connect-AzureAD
```

### Groups

```powershell
# List all Microsoft Entra ID groups that are synchronized from on-premises Active Directory.
Get-AzureADGroup -All $True | ?{$_.OnPremisesSecurityIdentifier -ne $Null}

# Get all attributes of a specific group.
Get-AzureADGroup -ObjectId $GroupObjectId | Format-List

# Search for groups based on their DisplayName.
Get-AzureADGroup -SearchString "$Whatever"

# List group members.
Get-AzureADGroupMember -ObjectId $GroupObjectId
```

### Roles

```powershell
# List all Microsoft Entra ID roles that are enabled.
Get-AzureADDirectoryRole

# List all role templates.
Get-AzureADDirectoryRoleTemplate

# Get all attributes of a specific role.
Get-AzureADDirectoryRole -ObjectId $RoleObjectId | Format-List

# Search for roles based on their DisplayName.
Get-AzureADDirectoryRole | ?{$_.DisplayName -match "$Whatever"}

#List role members.
Get-AzureADDirectoryRoleMember -ObjectId $RoleObjectId
```

### Users

In Microsoft Entra ID, `UserPrincipalName` for users are basically their email address.

```powershell
# List all Microsoft Entre ID users that are synchronized from on-premises Active Directory.
Get-AzureADUser -All $True | ?{$_.OnPremisesSecurityIdentifier -ne $Null}

# Get all attributes of a specific user.
Get-AzureADUser -ObjectId $UserPrincipalName | Format-List

# Search for users based on their DisplayName and UserPrincipalName.
Get-AzureADUser -SearchString "$Whatever"

# Get groups and roles a user belongs to.
Get-AzureADUserMembership -ObjectId $UserPrincipalName
```

## Recommendations

- [ ] Ensure Microsoft Entra ID enumeration is not possible for standard users from Microsoft Administration Portals, PowerShell and Microsoft Graph APIs, which is controlled by different permissions.