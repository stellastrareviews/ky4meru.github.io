---
layout: default
title: Microsoft SQL Enumeration
parent: Microsoft SQL
grand_parent: Databases
nav_order: 1
permalink: /databases/mssql/enumeration/
---

# Microsoft SQL Enumeration
{: .no_toc }

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

## Prerequisites

* Microsoft SQL service in running on the target machine (default port: 1433).

## Exploit

As an unauthenticated user, use [PowerUpSQL](https://github.com/NetSPI/PowerUpSQL) to enumerate Microsoft SQL instance.

```powershell
Import-Module PowerUpSQL.ps1
Get-SQLInstanceDomain
Get-SQLConnectionTest -Instance "$hostname,$port" | Format-List
Get-SQLServerInfo -Instance "$hostname,$port"
```

You can also use [SQLRecon](https://github.com/skahwah/SQLRecon).

```powershell
# Enumerate instances.
./SQLRecon.exe /enum:sqlspns

# Try to authenticate with the currently logged on user.
./SQLRecon.exe /a:wintoken /h:$hostname,$port /m:whoami

# Enumerate domain users that belong to a group with `SQL` in it.
Get-DomainGroup -Identity *SQL* | % { Get-DomainGroupMember -Identity $_.distinguishedname | select groupname, membername }
```

If you have credentials, you can use them like following.

```powershell
# Test authenticated connection on MSSQL database.
./NetExec.exe mssql $target -d $domain -u $username -p $password

# Connect to the database.
impacket-mssqlclient -u $domain/$username:$password@$target -windows-auth

# Connect with SQLRecon.
./SQLRecon.exe /a:windomain /d:$domain /u:$username /p:$password /h:$hostname,$port /m:whoami
```

One authenticated, you can query the database (with the CLI `SQL>` if you are using `impacket-mssqlclient`).

```powershell
Get-SQLQuery -Instance "$hostname,$port" -Query "select @@servername"
./SQLRecon.exe /a:wintoken /h:$hostname,$port /m:query /c:"select @@servername"
```

Use following querys to get useful information about the instance.

```sql
-- Get currently logged on user.
SELECT SYSTEM_USER;

-- Check if currently logged on user is sysadmin.
SELECT IS_SRVROLEMEMBER('sysadmin');

-- Check if xp_cmdshell is enabled.
SELECT value FROM sys.configurations WHERE name = 'xp_cmdshell';

-- Get principals with impersonation rights.
SELECT * FROM sys.server_permissions WHERE permission_name = 'IMPERSONATE';

-- Get principal IDs.
SELECT name, principal_id, type_desc, is_disabled FROM sys.server_principals;

-- Get links.
SELECT srvname, srvproduct, rpcout FROM master..sysservers;
```

You can also check context switching like so. Following command will list principal the currently logged on user can impersonate.

```powershell
./SQLRecon.exe /a:wintoken /h:$hostname,$port /m:impersonate
```