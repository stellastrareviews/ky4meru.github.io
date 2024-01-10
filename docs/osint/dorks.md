---
layout: default
title: Google Dorks
parent: Open Source Intelligence
nav_order: 1
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
# Limit search results to the DOMAIN
site:<$DOMAIN>

# Limit search results to specific file TYPE (ex: filetype:txt)
filetype:<$TYPE>

# Limit search results to specific extension (ex: ext:py, ext:xml, etc.)
ext:<$EXTENSION>

# Limit search results to pages that contain "MULTIPLE WORDS" in the title
intitle:"$MULTIPLE_WORDS"

# You can exclude with "-" as below
# Search pages on fruits.com containing "Love Fruits" in the title, excluding xml pages
site:cars.com -filetype:xml intitle:"Love cars"
```

## Useful links

* [DorkSearch](https://dorksearch.com/)
* [Google Hacking Database](https://www.exploit-db.com/google-hacking-database)

## Recommendations

- [ ] Keep sensitive information about you in your pocket, not on the Internet.