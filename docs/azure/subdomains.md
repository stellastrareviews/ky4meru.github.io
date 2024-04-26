---
layout: default
title: Subdomains Enumeration
parent: Azure
nav_order: 1
permalink: /azure/subdomains/
---

# Subdomains Enumeration
{: .no_toc }

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

## Prerequisites

* Internet connection.

## Exploit

Use [MicroBurst](https://github.com/NetSPI/MicroBurst) to enumerate subdomains.

```powershell
Import-Module .\MicroBurst.psm1
Invoke-EnumerateAzureSubDomains -Base $CompanyName -Verbose
```