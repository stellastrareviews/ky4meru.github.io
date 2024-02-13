---
layout: default
title: Git Repositories
parent: Open Source Intelligence
nav_order: 3
permalink: /osint/git/
---

# Git Repositories
{: .no_toc }

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

## Vulnerability

Sometimes, you'll find sensitive information in public git repositories like passwords, secrets, hashes, keys, usernames or emails. Since git is a versionning tool, such information are not necessarily in files, they could be in old commits that have aimed to delete them but still present and can be retrieved.

## Prerequisites

* Internet connection.

## Exploit

You can do it automatically...

```bash
# ...using gitleaks.
gitleaks detect -v -s $path
```

Or manually using following links.

## Useful links

* [GitLab](https://gitlab.com/)
* [GitHub](https://github.com/)
* [GitHub Gist](https://gist.github.com/)
* [SourceForge](https://sourceforge.net/)

## Recommendations

- [ ] Don't store plaintext secrets in git repositories, especially public ones.
- [ ] Instead, use vaults or key managers to securely manage secrets' lifecycle.