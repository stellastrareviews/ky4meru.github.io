---
layout: default
title: Domain Exposure
parent: Open Source Intelligence
nav_order: 1
permalink: /osint/domain/
---

# Domain Exposure
{: .no_toc }

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

## Vulnerability

You can learn a lot on the Internet on someone or a company with a simple domain name.

## Prerequisites

* Internet connection.

## Exploit

```bash
# Get DNS A records (public IP addresses) using dig.
dig $domain

# Get information about the domain using whois.
whois $ip
whois $domain
whois $domain -h $host

# Using theHarvester.
theHarvester -d $domain -b $source
```

You can also use [SpiderFoot](https://github.com/smicallef/spiderfoot) to search information on a company based on its name or domain.

```bash
spiderfoot -l 127.0.0.1:8000
```

You can actively learn more about a domain by [enumerating its subdomains](/network/subdomains/).

## Useful links

* [Netcraft](https://searchdns.netcraft.com/) to find domains and subdomains.
* [Shodan](https://www.shodan.io/) to find publicly exposed assets.

## Recommendations

- [ ] Nothing to do here.