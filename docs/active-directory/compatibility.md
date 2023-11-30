---
layout: default
title: Pre Windows 2000 Compatibility Abuse
parent: Active Directory
nav_order: 3
permalink: /ad/compatibility/
---

# Pre Windows 2000 Compatibility Abuse
{: .no_toc }

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

## Vulnerability

Domain joined computers that have the option *Pre Windows 2000 Compatibility* enabled have default credentials with the following format.

```bash
DOMAIN/COMPUTER$:COMPUTER
```

## Prerequisites

- Low privileged domain account.

## Exploit

To lookup on domain computers that have this option enabled, use `ldapsearch-ad`.

```bash
./ldapsearch-ad.py -l $DC_IP -d $DOMAIN -u $USERNAME -p $PASSWORD -t search -s '(userAccountControl=4128)'
```