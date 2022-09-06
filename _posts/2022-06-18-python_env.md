---
title: "[Python] 파이썬 가상환경 구성 with `pyenv`, `pyenv-virtualenv`"
excerpt: "pyenv, pyenv-virtualenv"
categories:
  - Python
tags:
  - pyenv
  - pyenv-virtualenv
last_modified_at: 2022-06-18
---

> 파이썬에는 다양한 패키지들이 존재하고, 꾸준히 업데이트가 진행되고 있다. 새로운 업데이트가 있을 때마다 매번 업데이트하고 변경하면 여러 패키지 간에 버전 호환 문제가 생길 수 있다. 또한, 프로젝트를 진행할 때마다 사용해야하는 패키지의 버전이 다를 수도 있다. 따라서 프로젝트마다 내가 사용할 가상환경을 따로 생성해 관리하는 것이 효율적이다.<br>
<br>
> 이 때, 파이썬 가상환경 구성을 도와주는 툴이 `pyenv`와 `pyenv-virtualenv`이다.
> + `pyenv`: 다양한 파이썬 버전을 설치하게 해준다.
> + `pyenv-virtualenv`: 프로젝트 별로 서로 다른 파이썬 환경을 구축하기 위해 가상환경을 생성하게 해준다.

<div class="notice--info" markdown="1">
💡 <b>Installation Environment</b>

  + MacOS
  + Zsh
</div>

# 1. `pyenv`

+ 파이썬 버전 관리 시스템 (Simple Python Version Manager)
+ 한 대의 컴퓨터에 여러개의 파이썬 버전을 설치하고 관리

## 📍 Installation

MacOS의 `Homebrew`로도 Pyenv를 설치할 수 있지만, `Basic GitHub Checkout` 방식으로 진행해보겠다.

<b>(step1)</b> Checkout pyenv
- 경로: 설치하고자하는 경로에 설치. `$HOME/.pyenv` 경로 추천.

```bash
$ git clone https://github.com/pyenv/pyenv.git ~/.pyenv
```

<b>(step2)</b> Set up shell environment for pyenv

```bash
$ echo 'export PYENV_ROOT="$HOME/.pyenv"' >> ~/.zshrc
$ echo 'command -v pyenv >/dev/null || export PATH="$PYENV_ROOT/bin:$PATH"' >> ~/.zshrc
$ echo 'eval "$(pyenv init -)"' >> ~/.zshrc
```

<br>

# 2. `pyenv-virtualenv`

+ 가상환경을 만들어주는 툴
+ 파이썬 버전과 라이브러리의 완전한 격리 환경 제공
+ virtualenv의 pyenv 확장 플러그인

## 📍 Installation

`pyenv`와 마찬가지로 `Homebrew`로도 설치할 수 있지만, `pyenv plugin`을 통해 설치해보겠다.

<b>(step1)</b> Checkout pyenv-virtualenv
```bash
$ git clone https://github.com/pyenv/pyenv-virtualenv.git $(pyenv root)/plugins/pyenv-virtualenv
```

<b>(step2)</b> Set up shell environment for pyenv-virtualenv

```bash
$ echo 'eval "$(pyenv virtualenv-init -)"' >> ~/.zshrc
```

<br>

# 3. Usage

## 1) `pyenv`

### List all avaliable versions of Python

```bash
$ pyenv versions --list
```

### Install desired Python version

```bash
$ pypenv install <version>
```

### Switch between Python versions
+ 현재 shell session에서의 Python 버전 선택

  ```bash
  $ pyenv shell <version>
  ```

+ 현재 디렉토리에서의 Python 버전 지정

  + Python 버전을 지정할 디렉토리 내부에서 아래 명령어 수행.

  + 아래 명령어 수행시, 현재 디렉토리에 명시한 Python 버전이 기록된 `.python-version` 파일이 생성된다. 앞으로 해당 파일이 저장된 디렉토리에 진입시, 파일 내에 입력된 python 환경으로 Python 버전이 지정된다.

  ```bash
  $ pyenv local <version>
  ```

+ 전역 Python 버전 지정

  ```bash
  $ pyenv global <version>
  ```

### Uninstall Python versions

```bash
$ pyenv uninstall <version>
```

### List all installed Python versions

```bash
$ pyenv versions
  system
  3.10.0
  3.10.0/envs/venv3_10_0
  3.10.5
* venv3_10_0 (set by /Users/jinsolkim/Desktop/.python-version)
```

### Display the currently active Python version

```bash
$ pyenv version
venv3_10_0 (set by /Users/jinsolkim/Desktop/.python-version)
```

<br>

## 2) `pyenv-virtualenv`

### Create a virtualenv for the Python version used with pyenv

```bash
$ pyenv virtualenv <version> <env_name>
```

### Create virtualenv from current version

```bash
$ pyenv version
3.10.0 (set by /Users/jinsolkim/Desktop/.python-version)
$ pyenv virtualenv venv3_10_0
```

### List existing virtualenvs

```bash
$ pyenv virtualenvs
  3.10.0/envs/venv3_10_0 (created from /Users/jinsolkim/.pyenv/versions/3.10.0)
* venv3_10_0 (created from /Users/jinsolkim/.pyenv/versions/3.10.0)
```

### Activate/Deactivate virtualenv

```bash
$ pyenv activate <env_name>
```

```bash
$ pyenv deactivate <env_name>
```

### Delete existing virrtualenv

```bash
$ pyenv uninstall <env_name>
```

```bash
$ pyenv virtualenv-delete <env_name>
```

<br>

# Reference

+ [pyenv github](https://github.com/pyenv/pyenv)
+ [pyenv-vertualenv github](https://github.com/pyenv/pyenv-virtualenv)