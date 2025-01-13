---
title: "[Git] SSH Key 생성 & GitHub 등록"
description: ""
author: aijinsol
date: 2022-09-06
categories:
  - Git
tags:
  - git
  - GithubPages
---

# 1. SSH Key 생성

- `ssh-keygen` 명령어를 통해 SSH key를 생성하면, `.ssh` 폴더가 생성되면서 그 안에 private key와 public key가 생성된다.
- key 생성시에 `-t` 옵션을 지정하여 key가 생성되는 알고리즘의 종류와 key의 크기(bit)를 지정할 수 있다.
    - `rsa` 알고리즘: 암호화 속도가 빠르고, 복호화 속도가 느리다.
    - `dsa` 알고리즘: 암호화 속도가 느리고, 복호화 속도가 빠르다.

```bash
ssh-keygen -t rsa
```

![fig1](/statics/posts/git/git-02-fig1.png){: width="550"}

![fig2](/statics/posts/git/git-02-fig2.png){: width="550"}

<br>

# 2. GitHub에 SSH public key 등록

- `.ssh` 폴더 내부의 `id_rsa.pub` 파일을 열어 기재되어 있는 public key를 복사하여 GitHub 페이지에 등록 (`Settings > SSH and GPG keys > New SSH Key`)

![fig3](/statics/posts/git/git-02-fig3.png){: width="550"}
