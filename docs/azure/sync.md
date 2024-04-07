---
layout: default
title: Synchronization Credentials Extraction
parent: Azure
nav_order: 1
permalink: /azure/sync/
---

# Synchronization Credentials Extraction
{: .no_toc }

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

## Vulnerability

Being local administrator of AD Connect server, you can dump `MSOL` and `SYNC` accounts' credentials, responsible to synchronize Active Directory and Azure Entra ID.

## Prerequisites

* Local administrator rights on AD Connect server.

## Exploit

To extract credentials, execute [DumpAADSyncCreds](https://github.com/Hagrid29/DumpAADSyncCreds) on AD Connect server.

```powershell
.\DumpAADSyncCreds.exe get_token
```

## Recommendations

- [ ] Enforce EDR on servers to detect and block malware usage.