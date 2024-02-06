---
layout: default
title: SMB Enumeration
parent: Network
nav_order: 1
permalink: /network/smb/
---

# SMB Enumeration
{: .no_toc }

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

## Vulnerability

TODO: Describe the vulnerability here.

## Prerequisites

* Network route to the network you want to scan.

## Exploit

```bash
# Using nmap ...
nmap -v -p 139,445 $target

# ... and its NSE scripts (ex: to guess OS thanks to SMB port).
ls -1 /usr/share/nmap/scripts/smb*
nmap -v -p 139,445 --script smb-os-discovery $target

# Using nbtscan.
sudo nbtscan -r $target

# Using net: list all shares running on target.
net view \\$target /all

# Using cme as null session, anonymous and guest.
crackmapexec smb $target -u '' -p '' --shares
crackmapexec smb $target -u 'a' -p '' --shares
crackmapexec smb $target -u 'guest' -p '' --shares

# Using smbmap as anonymous and guest.
smbmap -u '' -p '' -P 445 -H $target
smbmap -u 'guest' -p '' -P 445 -H $target

# Using smbclient as anonymous and guest.
smbclient -U'%' -L //$target
smbclient -U'guest%' -L //$target

# On a Windows computer, list currently used pipes.
ls \\.\pipe\
```

## Useful links

* TODO: List links here.

## Recommendations

- [ ] TODO: List recommendations here.