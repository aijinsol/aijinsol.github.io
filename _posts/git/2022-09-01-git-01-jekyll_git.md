---
title: "[Git] Git 기본 사용법 - for Jekyll blogging"
description: ""
author: aijinsol
date: 2022-09-01
categories:
  - Git
tags:
  - git
  - jekyll
---

# 1. Git config (name, email)

로컬에서 기본값으로 사용할 Git username과 email 설정하기 (global 옵션 지정)

```bash
git config --global user.name "{USER_NAME}"
git config --global user.email {EMAIL_ADDRESS}
```

위 코드를 실행하면 `~/.gitconfig` 파일이 생성되고 해당 파일을 열어보면 아래와 같이 git config 정보가 저장된다. 로컬에서 `git commit` 시에 `.gitconfig` 의 정보를 기본적으로 사용하게 된다.

![fig1](/statics/posts/git/git-01-fig1.png){: width="300"}

현재 저장소의 username, email 설정값 확인하기

```bash
git config user.name
git config user.email
```

<br>

# 2. Git 기본 명령어

## 1) `git init`

```bash
git init
```

- 프로젝트를 시작하고 소스코드를 Git으로 관리하기 위해서는 가장 먼저 `git init` 으로 repository를 초기화해주는 과정이 필요하다.
- `git init` 코드를 실행하면 `.git` 폴더가 생성된다. `.git` 은 repository로서 모든 변경 내용을 저장한다.

<br>

## 2) `git status`

```bash
git status
```

- `git status` : 어떤 파일이 tracked 또는 untracked 되어 있는지 파일의 상태를 알려준다(파일 상태 확인).
    - `nothing to commit, working directory clean` : 수정된 파일이 없음
    - `Untracked files` : Working directory에 존재하는 파일. 즉, `git add`하기 전 새로 생성되거나 변경된 파일
    - `Changes to be commited` : Staging area에 존재하는 파일. 즉, `git add`는 되었지만 아직 `git commit`이 되지 않은 파일
    - `Changes not staged for commit` : 기존에 `git add`가 진행된 파일이지만 파일에 변경사항이 생겨 새롭게 `git add`를 해야하는 파일

![fig2](/statics/posts/git/git-01-fig2.png){: width="600"}

<br>

## 3) `git add`

- `git add`: Working directory 상의 변경사항을 staging area에 추가하기 위해 사용
- `git commit` 명령어를 통해 실제로 변경사항을 적용하기 전에, `git add`를 사용하여 변경사항을 모아놓는다.

현재 directory 상의 모든 변경사항을 staging area에 추가

```bash
git add .
```

<br>

변경된 파일 모두를 staging area에 추가

```bash
git add -A
```

<br>

Working directory의 변경사항 중 일부만 staging area로 이동

```bash
git add {DIR/FILE_NAME}
```

<br>

## 4) `git commit`

- `git commit`: Staging area에 저장되어 있는 변경사항들을 local repository에 저장

```bash
git commit -m '{COMMIT_MESSAGE}'
```

<br>

## 5) `git push`

- local repository에 commit되어 있는 파일들을 모두 remote repository로 보내기 위해서 `push` 명령어 사용

나의 local repository와 remote repository 연결

```bash
git remote add origin {REMOTE_REPOSITORY_GITHUB_URL}
```

<br>

origin이라는 이름을 가진 remote repository의 master 브랜치에 소스코드 올리기

```bash
git push origin master
```
