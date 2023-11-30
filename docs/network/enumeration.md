---
layout: default
title: Network Enumeration
parent: Network
nav_order: 3
permalink: /network/enumeration/
---

# Network Enumeration
{: .no_toc }

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

## Exploit

```bash
# Using Netcat.
nc -nv -w 1 [-u] -z $TARGET $PORTS

# Using Nmap.
sudo nmap -sC -sV -oN nmap.out $TARGET [-p $PORTS] [-oX nmap.xml]
sudo nmap -O $TARGET --osscan-guess

# Using PowerShell.
Test-NetConnection $TARGET -Port $PORT
1..$PORT | % {echo ((New-Object Net.Sockets.TcpClient).Connect("$TARGET", $_)) "TCP port $_ is open"} 2>$null
```