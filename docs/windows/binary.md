---
layout: default
title: Binary Abuse
parent: Windows
nav_order: 1
permalink: /windows/binary/
---

# Binary Abuse
{: .no_toc }

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

## Vulnerability

Multiple default Windows binaries, scripts and libraries can be used by a non-privileged user for defensive evasion, privilege escalation or persistence if their permissions are not correctly set. 

## Prerequisites

* Low privileged access to your Windows target.

## Exploit

Simply go on [LOLBAS](https://lolbas-project.github.io/) and have fun.

## Recommendations

- [ ] Set appropriate permissions on default Windows binaries, scripts and libraries to avoid any abuse of their usage.