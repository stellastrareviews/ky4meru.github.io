---
layout: default
title: DMARC Record
parent: Open Source Intelligence
nav_order: 1
permalink: /osint/dmarc/
---

# DMARC Record
{: .no_toc }

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

## Vulnerability

Domain-based Message Authentication, Reporting and Conformance (DMARC) is an authentication protocol for emails. It prevents from abusing like performing email spoofing and protects domain against phishing, scams and other common email attacks. To enforce this authentication protocol, domain owner must publish a DMARC record in its DNS with a the submdomain label `_dmarc`. Since this information is public, you can retrieve it and check if the domain is correctly protected against phishing before doing anything.

## Prerequisites

* Internet connection.

## Exploit

To query the DMARC record for a specific domain, use following command on Linux or Windows.

```bash
nslookup -type=txt _dmarc.$domain
```

If you get something like `"DMARC1; p=none; ..."` as output, it means DMARC DNS entry is correctly published. Otherwise, you can abuse the domain to perform email spoofing and phishing.

## Useful links

* [Dmarcian](https://dmarcian.com/fr/dmarc-inspector/) to inspect DMARC record online.

## Recommendations

- [ ] Publish DMARC DNS entry for publicly exposed domains.