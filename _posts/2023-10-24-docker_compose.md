---
title: "[ETC] Docker-compose"
excerpt: ""
categories:
  - ETC
tags:
  - docker
  - docker-compose
last_modified_at: 2023-10-24
---

> Compose is a tool for defining and running multi-container Docker applications. With Compose, you use a YAML file to configure your application's services. Then, with a single command, you create and start all the services from your configuration.
>
> Compose works in all environments; production, staging, development, testing, as well as CI workflows. It also has commands for managing the whole lifecycle of your application:
>
> - Start, stop, and rebuild services
> - View the status of running services
> - Stream the log output of running services
> - Run a one-off command on a service
>
> by [Official Docker Docs](https://docs.docker.com/compose/)

# 1. Docker-compose는 무엇일까?

- `Docker-compose`는 여러 개의 docker container를 사용하는 application을 쉽게 관리할 수 있도록 도와준다.
- `docker-compose.yaml` 또는 `docker-compose.yml` 파일명의 파일로 작성해야한다.
- `docker-compose up` 명령어 하나로 파일에 정의한 서비스들이 한 번에 컨네이너로 실행되거나, `docker-compose down` 명령어로 정의한 서비스를 한 번에 내릴 수 있다.
- 참고) docker 프로그램 안에서도 docker compose 프로그램이 자체적으로 내장되어 있다. 하지만 보통 `docker-compose standalone` 버전으로 별도로 설치해서 많이 사용한다. docker 프로그램 내의 compose를 사용하기 위해서는 `docker compose up`, `docker compose down`과 같이 중간에 `-`만 빼주면 된다.

<br>

# 2. Docker-compose 설치

[Install Compose standalone](https://docs.docker.com/compose/install/standalone/) 공식 사이트에 설명되어 있는 설치 가이드를 따라간다.

- Step 1) 설치 명령어 실행

```bash
$ sudo curl -SL https://github.com/docker/compose/releases/download/v2.23.0/docker-compose-linux-x86_64 -o /usr/local/bin/docker-compose
```

`sudo` 명령어를 추가하지 않으면 설치가 안 될 수도 있다. `sudo` 명령어를 추가해주자.

<br>

- Step 2) 실행 권한 추가

```bash
$ sudo chmod +x /usr/local/bin/docker-compose
```

<br>

- Step 3) 설치 확인 (버전 확인)

```bash
$ docker-compose --version
```

<br>

![image01](/assets/images/2023-10-24-docker_compose_01.png){: .align-center}{: width="80%" height="80%"}
