---
layout: default
title: AppLocker Bypass
parent: Active Directory
nav_order: 1
permalink: /ad/applocker/
---

# AppLocker Bypass
{: .no_toc }

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

## Vulnerability

If [AppLocker](/ad/enumeration/#applocker) is enabled on the target computer - *or even domain* - it exists multiple ways to bypass it that are explained here.

## Prerequisites

* [AppLocker](/ad/enumeration/#applocker) is enabled on the target computer.

## Exploit

By default, AppLocker rules allow the execution of any binary as long as it is in (including subdirectories):
* `C:\Program Files\`.
* `C:\Windows\`.

To list `C:\Windows\` subdirectories permissions, use following command. For instance, `C:\Windows\Tasks` is by default writable for any user.

```powershell
Get-ChildItem -Path "C:\Windows\*" | where-object {($_.PsIsContainer)} | Get-Acl
```

You can also take a look at AppLocker policy to check if some `FilePathCondition` could be abused because of wildcard usage.

Finally, you can [abuse Windows binaries](/windows/binary/) to execute payloads bypassing AppLocker.

## Useful links

* [UltimateAppLockerByPassList](https://github.com/api0cradle/UltimateAppLockerByPassList) GitHub repository.