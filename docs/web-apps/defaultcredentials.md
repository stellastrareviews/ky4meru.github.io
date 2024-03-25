---
layout: default
title: Default Credentials
parent: Web Applications
nav_order: 1
permalink: /webapps/defaultcredentials/
---

# Default Credentials
{: .no_toc }

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

## Vulnerability

Some people think it is a good idea to let default - *and publicly documented* - credentials on web applications that are provided at installation time. With a quick search, you could find these credentials and gain an initial foothold on the application without too much effort - *nor coffee*.

## Prerequisites

* Having a network access to the web application.

## Exploit

To exploit this vulnerability, simply search for `<WEBAPP> default credentials` or `<WEBAPP> default admin password` on your favorite web browser and attempt results on the target. You can also try to use [Default Credentials Cheat Sheet](https://github.com/ihebski/DefaultCreds-cheat-sheet) which centralize a huge number of default credentials on many technologies.

## Recommendations

- [ ] Change default credentials at or after installation.