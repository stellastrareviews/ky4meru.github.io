---
layout: default
title: User Spraying
parent: Active Directory
nav_order: 1
permalink: /ad/userspraying/
---

# User Spraying
{: .no_toc }

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

## Vulnerability

Kerberos protocol implementation makes possible to request a TGT as soon as you know the username of a domain user. If you get an AS-REP, it means the domain user exists. You can leverage this behavior to perform user spraying.

## Prerequisites

* Having a network access to the Domain Controller.
* Kerberos protocol is enabled (port 88 by default).

## Exploit

To perform this attack, use [Kerbrute](https://github.com/ropnop/kerbrute).

```bash
kerbrute userenum -d $Domain --dc $DomainControllerIP usernames.txt
```