---
layout: default
title: User Spraying
parent: Azure
nav_order: 1
permalink: /azure/userspraying/
---

# User Spraying
{: .no_toc }

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

## Vulnerability

Knowing the domain name associated to an Azure tenant, you can perform a spraying attack to identify existing users.

## Prerequisites

* Internet connection.

## Exploit

Use [O365Spray](https://github.com/0xZDH/o365spray).

```bash
# First, validate the domain exists.
o365spray --validate --domain $DomainName

# Then, perform user spraying.
o365spray --enum -U Usernames.txt --domain $DomainName
```

## Recommendations

- [ ] Nothing to do here, this exploit is possible by design.