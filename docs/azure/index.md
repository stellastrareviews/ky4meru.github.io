---
layout: default
title: Azure
nav_order: 4
has_children: true
permalink: /azure/
has_toc: false
---

# Azure
{: .no_toc }

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

## Recon

First, check if the Azure domain exists.

```bash
wget https://login.microsoftonline.com/$DomainName/.well-known/openid-configuration
```

## Without an account

* [Synchronization Credentials Extraction](/azure/sync/)
* [User Spraying](/azure/userspraying/)

## Privilege escalation

* [Applications Rights Abuse](/azure/apps/)

## Useful links

- [AzureHound](https://github.com/BloodHoundAD/AzureHound) to enumerate Azure Active Directory.