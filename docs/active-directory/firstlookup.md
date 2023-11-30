---
layout: default
title: Active Directory First Lookup
parent: Active Directory
nav_order: 6
permalink: /ad/lookup/
---

# Active Directory First Lookup
{: .no_toc }

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

## Exploit

```bash
# Get domain name.
nmcli dev show $INTERFACE

# Get domain controllers IP.
nslookup $DOMAIN

# Get domain controllers system info.
cme smb $DC_IPS

# Authenticate as Guest and anonymous.
cme smb $DC_IP -d $DOMAIN -u 'Guest' -p ''
cme smb $DC_IP -d $DOMAIN -u 'Guest' -p '' --local-auth
cme smb $DC_IP -d $DOMAIN -u '' -p ''
cme smb $DC_IP -d $DOMAIN -u '' -p '' --local-auth

# List shares as anonymous.
cme smb $DC_IP -d $DOMAIN -u '' -p '' --shares
```