---
title: "[ETC] `sudo` 없이 `docker` 명령어 사용하기"
description: ""
author: aijinsol
date: 2023-10-24
categories:
  - ETC
tags:
  - docker
---

> Environment: Linux (Ubuntu ver 20.04)

# sudo 명령어 없이 docker 명령어 사용하기 설정

- Step 1) 현재 `${USER}`를 docker 그룹에 추가

  ```bash
  sudo usermod -aG docker ${USER}
  ```

  - `docker`는 기본적으로 `root` 권한이 필요하다. `root`가 아닌 `user`가 `sudo` 명령어 없이 사용하기 위해서는 해당 `user`를 `docker` 그룹에 추가해야 한다.

<br>

- Step 2) 터미널 종료 후 재접속
  - 터미널을 종료했다가 다시 `ssh` 명령어를 통해 Linux 환경으로 접속한다.

<br>

- Step 3) 현재 `${USER}`가 docker 그룹에 포함되었는지 확인

  ```bash
  id -nG
  ```

  - 출력결과에 `docker`가 나타나면 현재 `${USER}`가 docker 그룹에 포함된 것! <br>
    이제 `sudo docker` 명령어 대신 `docker` 명령어로도 관리자 권한으로 실행된다!

<br>

![fig1](/statics/posts/etc/etc-02-fig1.png){: width="550"}
