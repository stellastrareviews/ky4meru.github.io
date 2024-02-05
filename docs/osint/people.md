---
layout: default
title: People
parent: Open Source Intelligence
nav_order: 5
permalink: /osint/people/
---

# People
{: .no_toc }

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

## Vulnerability

TODO: Describe the vulnerability here.

## Prerequisites

* Knowing email or username your target uses.

## Exploit

If you know a username of your target, use [Maigret](https://github.com/soxoj/maigret) to identify where this username is used.  

```bash
maigret $username
```

Otherwise, if you know an email of your target, use [Holehe](https://github.com/megadose/holehe) or [H8mail](https://github.com/khast3x/h8mail) to identify on which websites this email is used.

```bash
holehe $email
h8mail -t $email
```

You can also use [SpiderFoot](https://github.com/smicallef/spiderfoot) to search information on someone based on any initial information: name, email, phone, etc.

```bash
spiderfoot -l 127.0.0.1:8000
```

## Useful links

* [Hunter.io](https://hunter.io/) to find email patterns.

## Recommendations

- [ ] TODO: List recommendations here.