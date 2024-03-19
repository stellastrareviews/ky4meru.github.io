---
layout: default
title: Obfuscation
parent: Antivirus
nav_order: 1
permalink: /antivirus/obfuscation/
---

# Obfuscation
{: .no_toc }

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

## Antivirus bypass

- [Frankenstein](https://github.com/dotPY-hax/frankenstein-obfuscator)
- [Invoke-Stealth](https://github.com/JoelGMSec/Invoke-Stealth)
- [Veil](https://github.com/Veil-Framework/Veil)

## EDR bypass

- [Freeze](https://github.com/Tylous/Freeze)
- [Inceptor](https://github.com/klezVirus/inceptor)
- [Nimcrypt2](https://github.com/icyguider/Nimcrypt2)
- [Pyramid](https://github.com/naksyn/Pyramid)

## Tips and Tricks

To check if a file has the Mark of the Web (MOTW).

```powershell
Get-Content -Path $file -Stream Zone.Identifier
```

It exists 5 zones, in trust order:
0. Local computer
1. Local intranet
2. Trusted sites
3. Internet
4. Restricted sites