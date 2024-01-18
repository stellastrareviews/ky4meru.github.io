---
layout: default
title: Subdomains Enumeration
parent: Network
nav_order: 5
permalink: /network/subdomains/
---

# Subdomains Enumeration
{: .no_toc }

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

## Vulnerability

When you are targeting a domain, it could be interesting to identify associated subdomains to either find useful information or to bounce from it once you will compromise it. Basically, finding subdomains could be done in two ways: active or passive. Active approach simply consists in brute forcing subdomains with a wordlist. Passive approach, which is faster and stealthier, consists in requesting public passive sources. 

## Prerequisites

* Internet connection.

## Exploit

You can enumerate subdomains in multiple ways.

```bash
# Passively using subfinder on Kali.
subfinder -d $domain

# Get IP adresses corresponding to a domain.
host $domain
host -t $type $domain

# Get domain corresponding to IP address.
nslookup $ip
nslookup -type=$type $ip $host

# Custom subdomain enumeration.
for sub in $(cat subdomains.txt); do host $sub.$domain; done

# Custom IP enumeration.
for ip in $(seq 200 254); do host 51.222.169.$ip; done

# Using DNSRecon.
dnsrecon -d $domain -t std
dnsrecon -d $domain -t brt -D $dict

# Using DNSEnum
dnsenum $domain
```

## Recommendations

- [ ] Nothing to do here, just harden components exposed on the Internet.