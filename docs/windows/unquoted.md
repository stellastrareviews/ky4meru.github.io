---
layout: default
title: Unquoted Service Paths
parent: Windows
nav_order: 13
permalink: /windows/unquoted/
---

# Unquoted Service Paths
{: .no_toc }

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

## Vulnerability

Windows services are basically executables that are automatically run by the system. They are configured with a path. If this path contains spaces that are not quoted, the operating system will interpret them as terminator characters. For instance, given the unquoted path `C:\Program Files\My Folder\Hello World.exe`, Windows will try to run in the order:

1. `C:\Program.exe`
2. `C:\Program Files\My.exe`
3. `C:\Program Files\My Folder\Hello.exe`
4. `C:\Program Files\My Folder\Hello World.exe`

If you have permissions to create an executable in one of the 3 first paths, you could execute whatever you want to escalate your privileges on the system.

## Prerequisites

* Low privileged access to the targeted system.

## Exploit

First, you need to identified services with unquoted paths that could be exploited.

```powershell
# Enumerate service paths using wmic.
wmic service get name, pathname

# Enumerate services using native PowerShell.
Get-CimInstance -ClassName win32_service | Select-Object Name, PathName
```

Once identified, you need to check if you have the correct permissions to drop a payload in either of possible paths.

```powershell
Get-Acl -Path "C:\Program Files\My Folder" | Format-List
```

If a group you belongs to has necessary permissions (i.e. *CreateFiles* or *FullControl*) to drop an executable, you can perform the attack. Once your executable in place, you can either restart the associated process (if you are local administrator) or wait for a system reboot.

If you're too lazy (and I know you are), you can use [SharpUp](https://github.com/GhostPack/SharpUp) or [WinPEAS](https://github.com/carlospolop/PEASS-ng) to automatically enumerate unquoted service paths that can be exploited.

```powershell
.\SharpUp.exe audit UnquotedServicePath
.\WinPEAS.exe
```

## Recommendations

- [ ] Quote Windows service paths with spaces.