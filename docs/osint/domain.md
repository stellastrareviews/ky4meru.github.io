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
# Using whois
whois $domain
whois $domain -h $host

# Using theHarvester
theHarvester -d $domain -b $source
```

## Useful links

* [Netcraft](https://searchdns.netcraft.com/) to find domains and subdomains.
* [Shodan](https://www.shodan.io/) to find publicly exposed assets.

## Recommendations

- [ ] Nothing to do here.