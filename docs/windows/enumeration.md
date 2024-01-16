---
layout: default
title: Windows Enumeration
parent: Windows
nav_order: 3
permalink: /windows/enumeration/
---

# Windows Enumeration
{: .no_toc }

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

## Automated enumeration

```bash
# Using WinPEAS
winPEAS.exe

# Using Seatbelt locally
Seatbelt.exe -group=all -full -outfile="C:\Temp\out.txt"

# Using Seatbelt remotely
Seatbelt.exe -group=system-computername=$target -username=$domain\$username -password="$password"

# Using JAWS
powershell.exe -ExecutionPolicy Bypass -File .\jaws-enum.ps1 -OutputFilename JAWS-Enum.txt

# Using WesNG
systeminfo > systeminfo.out
wes.py systeminfo.out

# Using Enum4Linux
enum4linux-ng $target -u $username -p $password
```

## Manual enumeration

Don't forget to take a loot at [Antivirus Enumeration](/antivirus/enumeration/) page to list active antivirus and EDRs on the Windows host.

```bash
# Get groups of current logged on user
whoami /groups

# Get privileges of current logged on user
whoami /priv

# Get system information
systeminfo

# Get network configuratio
ipconfig /all

# Get routing table
route print

# Get listening ports (-a: active connections, -n: disable name resolution, -o: PID)
netstat -ano

# Get running processes
Get-Process

# Get installed applications (32-bits then 64-bits)
Get-ItemProperty "HKLM:\SOFTWARE\Wow6432Node\Microsoft\Windows\CurrentVersion\Uninstall\*" | select displayname
Get-ItemProperty "HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\Uninstall\*" | select displayname

# Search for useful files that might contains sensitive information
Get-ChildItem -Path C:\Users\$username\ -Include *.txt,*.pdf,*.xls,*.xlsx,*.doc,*.docx -File -Recurse -ErrorAction SilentlyContinue

# Get history of PowerShell
(Get-PSReadlineOption).HistorySavePath
type <PATH>

# Get AppLocker enforced policy.
Get-AppLockerPolicy -Effective -XML

# Get environment variables
Get-Children -Path Env:
```