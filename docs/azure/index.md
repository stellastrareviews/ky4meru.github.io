---
layout: default
title: Azure
nav_order: 2
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

## Without an account

First, check if the Azure domain exists.

```bash
wget https://login.microsoftonline.com/$DOMAIN/.well-known/openid-configuration
```