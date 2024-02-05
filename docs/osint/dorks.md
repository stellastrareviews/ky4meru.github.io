---
layout: default
title: Google Dorks
parent: Open Source Intelligence
nav_order: 2
permalink: /osint/dorks/
---

# Google Dorks
{: .no_toc }

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

## Vulnerability

Google Dorking (or Google Hacking) is a way of using Google Search engine to find information on the Internet to find sensitive information or vulnerabilities.

## Prerequisites

* Internet connection and your favorite browser with Google Search engine.

## Exploit

```bash
# Limit search results to the domain.
site:$domain

# Limit search results to specific file type (ex: filetype:txt).
filetype:$type

# Limit search results to specific extension (ex: ext:py, ext:xml, etc.).
ext:$extension

# Limit search results to pages that contain "word" in the title.
intitle:$word

# Limit search resulsts to pages that contains "word" in the content.
intext:$word

# Limit search resulsts to pages that contains "word" in the URL.
inurl:$word

# You can exclude with "-" as below.
# Search pages on cars.com containing "Peugeot 4ever" in the title, excluding xml pages.
site:cars.com -filetype:xml intitle:"Peugeot 4ever"
```

## Useful links

* [DorkSearch](https://dorksearch.com/)
* [Google Hacking Database](https://www.exploit-db.com/google-hacking-database)

## Recommendations

- [ ] Keep sensitive information about you in your pocket, not on the Internet.