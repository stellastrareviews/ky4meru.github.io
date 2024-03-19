---
layout: default
title: AV & EDR Enumeration
parent: Antivirus
nav_order: 2
permalink: /antivirus/enumeration/
---

# AV & EDR Enumeration
{: .no_toc }

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

## Vulnerability

Once authenticated on a Windows host, even low privileged, you can enumerate running antivirus engines and EDRs.

## Prerequisites

* Low privileged access to the targeted host.

## Exploit

Following commands can help you to enumerate antivirus protections on a Windows host.
```powershell
# With WMIC through cmd.
wmic /Node:localhost /Namespace:\\root\SecurityCenter2 Path AntivirusProduct

# With PowerShell.
Get-CimInstance -Namespace root/SecurityCenter2 -ClassName AntivirusProduct

# Manually looking for software in running services.
Get-Service | Select-Object -Property * | Select-String SentinelOne
```

If Windows Defender is running, take a look at how to [bypass](/antivirus/windefend/) it.

```powershell
# Check Windows Defender status.
Get-Service WinDefend
```

You can also use [EICAR anti malware testfile](https://www.eicar.org/download-anti-malware-testfile). Most of market products are designed to trigger this harmless file. Just download it, drag and drop on the targeted host (Windows or Linux) and wait to see if something happen (file is deleted, alert is triggered, etc).

## Useful links

* [EICAR](https://www.eicar.org/): European Institute for Computer Anti-Virus Research.