---
layout: default
title: SNMP Enumeration
parent: Network
nav_order: 12
permalink: /network/snmp/
---

# SNMP Enumeration
{: .no_toc }

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

## Vulnerability

SNMP (Simple Network Management Protocol) allows to monitor devices, based on the MIB (Management Information Base). You can use this protocol to enumerate useful information on a targeted computer using the corresponding MIB value.

Consider it exists two types of SNMP:
- **SNMPv1/2/2c**: uses community string as authentication and sends traffic in plaintext.
- **SNMPv3**: uses stronger authentication and encrypts traffic.

## Prerequisites

* Target must have SNMP service running (default port: 161).

## Exploit

{: .important }
> Before starting, and as recommended on [HackTricks](https://book.hacktricks.xyz/network-services-pentesting/pentesting-snmp#enumerating-snmp) book, it is **highly** recommended to run following commands to have more details about gathered OID.
> ```bash
> sudo apt install snmp-mibs-downloader
> sudo download-mibs
> sudo vi /etc/snmp/snmp.conf # Comment the line "mibs :"
> ```

```bash
# Check if SNMP port is opened with nmap.
sudo nmap -sU -p161 $target -v

# Brute-force communities using onesixtyone.
onesixtyone -c /usr/share/wordlists/seclists/Discovery/SNMP/snmp-onesixtyone.txt -i $target_file

# Enumerate OID using snmpwalk.c
snmpwalk -c $community -v$version -t 10 $target $mib

# Example: enumerate Windows users (corresponding MIB value).
snmpwalk -c public -v1 192.168.1.42 1.3.6.1.4.1.77.1.2.25

# Prefer using extended MIB!
snmpwalk -c public -v1 $target NET-SNMP-EXTEND-MIB::nsExtendObjects
```

Following table represents MIB values for Windows SNMP.

| MIB Value              | Description      |
|------------------------|------------------|
| 1.3.6.1.2.1.25.1.6.0   | System Processes |
| 1.3.6.1.2.1.25.4.2.1.2 | Running Programs |
| 1.3.6.1.2.1.25.4.2.1.4 | Processes Path   |
| 1.3.6.1.2.1.25.2.3.1.4 | Storage Units    |
| 1.3.6.1.2.1.25.6.3.1.2 | Software Name    |
| 1.3.6.1.4.1.77.1.2.25  | User Accounts    |
| 1.3.6.1.2.1.6.13.1.3   | TCP Local Ports  |

## Useful links

* [OID Info](http://www.oid-info.com/cgi-bin/display?tree=) to navigate through OIDs in MIB hierarchy.

## Recommendations

- [ ] Filter SNMP port when possible.
- [ ] Use SNMPv3 to enforce strong authentication and encryption.