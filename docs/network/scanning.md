---
layout: default
title: Ports Scanning
parent: Network
nav_order: 3
permalink: /network/scanning/
---

# Ports Scanning
{: .no_toc }

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

## Prerequisites

* Network route to the network you want to scan.

# Netcat

```bash
# -u for UDP scan.
nc -nv -w 1 [-u] -z $target $ports
```

## Nmap

{: .important }
> Don't forget to use `-sS` option in every nmap command to be more stealthy.

```bash
# Get open ports on target.
# Add -Pn for ping probe if needed.
ports=$(nmap -p- --min-rate=1000 -T4 $target | grep ^[0-9] | cut -d '/' -f1 | tr '\n' ',' | sed s/,$//)

# Get services running on open ports.
nmap -sC -sV $target -p$ports -oG nmap.out

# Get fingerprint of service running on web port.
sudo nmap -p$ports --script=http-enum $target

# Get OS guess, must be sudo.
sudo nmap -O $target --osscan-guess

# From a list of targets.
nmap -iL $filename [OPTIONS]
```

## Masscan and Aquatone

[Aquatone](https://github.com/michenriksen/aquatone) allows to get a screenshot of exposed web application during an automated network scan. It can be very useful to quickly identify web applications in your testing scope. To be as efficient as possible, you should combine its usage with [Masscan](https://github.com/robertdavidgraham/masscan).

```bash
# Add --max-rate option if you want to boost your scan. 
masscan $target -p- -oX out.xml [--max-rate 100000]

# Inject output into aquatone.
# Screenshots are located in ./aquatone/screenshots/.
cat out.xml | aquatone -nmap -out ./aquatone
```

## PowerShell

```bash
# For one port only.
Test-NetConnection $target -Port $port

# For 1 to N ports.
1..$port | % {echo ((New-Object Net.Sockets.TcpClient).Connect("$target", $_)) "TCP port $_ is open"} 2>$null
```

# Useful links

- [Gowitness](https://github.com/sensepost/gowitness) for an alternative to Aquatone.
- [RustScan](https://github.com/RustScan/RustScan) for another port scanner.
- [Threader3000](https://github.com/dievus/threader3000) for **again** another port scanner.