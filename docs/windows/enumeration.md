---
layout: default
title: Windows Enumeration
parent: Windows
nav_order: 5
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

{: .warning }
> Don't forget to take a look at [Antivirus Enumeration](/antivirus/enumeration/) page to list active antivirus and EDRs on the Windows host before dropping any tool to avoid being detected! Otherwise, take your chance with [Manual Enumeration](/windows/enumeration/#manual-enumeration).

### WinPwn

Using [WinPwn](https://github.com/S3cur3Th1sSh1t/WinPwn).

```powershell
Import-Module .\WinPwn.ps1
```

### WinPEAS

Using [WinPEAS](https://github.com/carlospolop/PEASS-ng/tree/master/winPEAS).

```powershell
.\winPEAS.exe
.\winPEAS.ps1
```
### Seatbelt

Using [Seatbelt](https://github.com/GhostPack/Seatbelt).

```powershell
# Using Seatbelt locally.
Seatbelt.exe -group=all -full -outfile="C:\Temp\out.txt"

# Using Seatbelt remotely.
Seatbelt.exe -group=system-computername=$target -username=$domain\$username -password="$password"
```

### JAWS

Using [JAWS](https://github.com/411Hall/JAWS).

```powershell
powershell.exe -ExecutionPolicy Bypass -File .\jaws-enum.ps1 -OutputFilename JAWS-Enum.txt
```

### Wes-NG

Using [Wes-NG](https://github.com/bitsadmin/wesng).

```powershell
systeminfo.exe > systeminfo.out
wes.py systeminfo.out
```

### Sherlock and Watson

Using [Sherlock](https://github.com/rasta-mouse/Sherlock) and its successor [Watson](https://github.com/rasta-mouse/Watson).

```powershell
# With Sherlock.
Import-Module .\Sherlock.ps1
Find-MS10092

# With Watson.
.\Watson.exe
```

## Manual enumeration

This section references who to manually enumerate a Windows host using one or more of the following methods (in the order):
* Using Windows builtins that can be invoked via `cmd.exe`.
* Using native PowerShell.
* Using - *your best friend* - [PowerView](https://github.com/PowerShellMafia/PowerSploit/).

### Environment variables

```powershell
set
Get-ChildItem -Path Env:
```

### Files

```powershell
# Search for useful files that might contains sensitive information.
Get-ChildItem -Path C:\Users\$username\ -Include *.txt,*.pdf,*.xls,*.xlsx,*.doc,*.docx -File -Recurse -ErrorAction SilentlyContinue

# Search for KDBX database.
Get-ChildItem -Path C:\ -Include *.kdbx -File -Recurse -ErrorAction SilentlyContinue
```

### Installed softwares

```powershell
reg query "HKLM\SOFTWARE\Wow6432Node\Microsoft\Windows\CurrentVersion\Uninstall\"
reg query "HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Uninstall\"
Get-ItemProperty "HKLM:\SOFTWARE\Wow6432Node\Microsoft\Windows\CurrentVersion\Uninstall\*" | select displayname
Get-ItemProperty "HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\Uninstall\*" | select displayname
```

### Network

```powershell
# Get network configuration.
ipconfig /all

# Get routing table.
route print

# Get listening ports (-a: active connections, -n: disable name resolution, -o: PID).
netstat -ano
```

### PowerShell history

Beware, sometimes the history file has another name than `ConsoleHost_history.txt`.

```powershell
dir %APPDATA%\Microsoft\Windows\Powershell\PSReadLine\
type %APPDATA%\Microsoft\Windows\Powershell\PSReadLine\ConsoleHost_history.txt
$(Get-PSReadlineOption).HistorySavePath
Get-Content -Path $Env:APPDATA\Microsoft\Windows\PowerShell\PSReadLine\ConsoleHost_history.txt
```

### Processes

```powershell
# List running processes.
tasklist /FI "STATUS eq RUNNING"
wmic process get ProcessID,ExecutablePath
Get-Process
ps
```

### Services

```powershell
# List all services, including stopped ones.
sc query state= all
Get-Service
Get-CimInstance -ClassName win32_service | Where-Object {$_.State -like "Running"}

# List running services.
sc query
Get-Service | Where-Object {$_.Status -eq "Running"}
Get-CimInstance -ClassName win32_service | Where-Object {$_.State -like "Running"}

# Get a specific service.
sc query $ServiceName
Get-Service $ServiceName
Get-CimInstance -ClassName win32_service | Where-Object {$_.Name -like "$ServiceName"}

# Get executable name executed by a service.
tasklist /fi "SERVICES eq $ServiceName"

# Get full path of an executable executed by a service.
wmic process where "name='$ExecutableName'" get ExecutablePath
```

### System

```powershell
systeminfo
Get-ComputerInfo
```

### Users

```powershell
# Get info about logged on user.
whoami /all

# Get info about a local user.
net user $username
```