---
title: "[Python] Python 개발 환경 관리"
description: ""
author: aijinsol
date: 2024-09-29
categories:
  - Python
tags:
  - homebrew
  - pip
  - pipx
  - poetry
  - pyenv
---

Python 개발을 시작하면서 다양한 도구들을 접하게 된다. 특히, <u><b>시스템 전역 패키지 매니저</b></u>인 `homebrew`, Python <b><u>패키지 관리 도구</u></b>인 `pip`, `pipx`, `poetry`, 그리고 Python <u><b>버전 매니저</b></u>인 `pyenv` 등은 각각 다른 역할을 하며, 처음에는 혼란스러울 수 있다. 이러한 도구들이 각각 어떤 역할을 하는지, 그리고 어떻게 연동되어 사용되는지 직관적으로 정리해 보자!

## 1. `homebrew`: 시스템 전역 패키지 매니저

- 역할:
    - Homebrew는 macOS와 Linux에서 동작하는 시스템 전역 패키지 매니저
    - 다양한 소프트웨어(예: Python, Node.js, Git, VSCode)를 설치하고 관리할 수 있으며, 의존성을 자동으로 해결해 주어 편리하게 패키지를 관리한다.

- 주요 기능:
    - 시스템에 설치된 소프트웨어를 쉽게 설치, 제거, 업데이트할 수 있다.
    - Python을 포함한 다양한 개발 도구 및 라이브러리 설치에 사용된다.

- 예시:
    - `brew install python`: 시스템 전역에 Python 설치
    - `brew install pipx`: pipx를 설치하여 Python CLI 도구를 관리

<br>

## 2. `pip`: Python 패키지 매니저

- 역할:
    - pip는 Python 패키지 매니저로, Python 패키지(라이브러리)를 프로젝트 내부 또는 <b>전역적</b>으로 설치하고 관리하는 도구
    - Python 프로젝트에서 사용할 다양한 라이브러리를 쉽게 설치할 수 있다.

- 주요 기능:
    - 패키지를 설치하거나 업데이트하고, 프로젝트에 필요한 라이브러리를 쉽게 추가한다.
    - 주로 라이브러리 설치에 사용된다. Ex) numpy, opencv

- 예시:
    - `pip install numpy`: numpy 라이브러리 설치
    - `pip install requests`: requests 라이브러리 설치

<br>

## 3. `pipx`: Python CLI 도구 관리용 패키지 매니저

- 역할:
    - pipx는 Python으로 만들어진 CLI(Command Line Interface) 도구를 독립된 가상 환경에 설치하고 관리할 수 있는 도구
    - 이를 통해 시스템 환경과 충돌 없이, CLI 도구를 전역적으로 설치하고 사용할 수 있다.

- 주요 기능:
    - 각 CLI 도구마다 독립적인 가상 환경을 만들어, 충돌 없이 CLI 도구를 관리한다.
    - CLI 도구를 설치할 때 사용된다. Ex) black, flake8, poetry

- 가상 환경 설명:
    - CLI 도구 전용 가상 환경을 만들어서, 해당 도구를 시스템 전역에서 안전하게 사용할 수 있도록 한다.
    - Ex) poetry를 설치하면, poetry 전용 가상 환경이 만들어져 다른 패키지들과의 충돌을 방지한다.

- 예시:
    - `pipx install poetry`: poetry라는 프로젝트 의존성 관리 도구 설치
    - `pipx install black`: black이라는 Python 코드 포매터 설치

<br>

## 4. `poetry`: Python 프로젝트 의존성 및 패키지 관리 도구

- 역할:
    - poetry는 Python 프로젝트에서 의존성 관리와 패키지 배포를 도와주는 도구
    - 프로젝트별로 가상 환경을 자동으로 생성하고, 해당 프로젝트의 모든 패키지 의존성을 관리한다.

- 주요 기능:
    - 프로젝트 내 가상 환경을 생성하고, 의존성을 명시하여 프로젝트에 필요한 패키지를 관리한다.
    - pyproject.toml 파일을 통해 패키지 버전과 의존성을 관리하여, 패키지 충돌을 방지한다.

- 가상 환경 설명:
    - poetry는 각 프로젝트별로 독립된 가상 환경을 자동으로 생성하여, 다른 프로젝트와 충돌하지 않도록 의존성을 관리한다.
    - Ex) 한 프로젝트에서는 Python 3.11를 사용하고, 다른 프로젝트에서는 Python 3.12을 사용할 때도 문제가 발생하지 않도록 각 가상 환경을 만든다.

- 예시:
    - `poetry new my_project`: 새로운 Python 프로젝트 생성
    - `poetry add requests`: requests 라이브러리를 프로젝트에 추가

<br>

## 5. `pyenv`: Python 버전 매니저

- 역할:
    - pyenv는 여러 버전의 Python을 시스템에 설치하고, 원하는 버전으로 전환할 수 있는 Python 버전 관리 도구
    - 프로젝트마다 다른 Python 버전이 필요할 때, Python 버전을 쉽게 전환하여 사용할 수 있도록 해준다.

- 주요 기능:
    - 다양한 버전의 Python을 설치하고, 시스템 전역 또는 프로젝트에서 사용하고 싶은 버전을 선택할 수 있다.
    - Poetry와 함께 사용하면, 프로젝트마다 Python 버전을 다르게 설정할 수 있다. ⭐

- 예시:
    - `pyenv install 3.12.5`: Python 3.12.5 버전 설치
    - `pyenv global 3.12.5`: 시스템 전역에서 Python 3.12.5을 사용하도록 설정

<br>

## 6. `pip`, `pipx`, `poetry` 간 관계

### 1) `pip`와 `pipx`

- pip은 일반적으로 라이브러리(예: numpy, opencv)를 설치할 때 사용된다.
- pipx는 Python 기반의 CLI 도구(예: black, flake8, poetry)를 독립된 가상 환경에 설치하여, 시스템 패키지와의 충돌을 방지하고 전역에서 사용할 수 있게 해준다.

### 2) `pipx`와 `poetry`

- pipx로 poetry를 설치하면, poetry가 독립된 가상 환경에 설치되어 시스템 전역에서 사용 가능해진다.
- 이후, poetry로 프로젝트를 생성하면, 그 프로젝트에 맞는 또 다른 가상 환경이 생성된다.
- ❗ 이 두 가상 환경은 서로 포함 관계가 아니며, 독립적인 환경으로 존재한다. 즉, pipx의 가상 환경에 설치된 poetry는 poetry로 만든 프로젝트 가상 환경과 독립적으로 동작한다. ⭐

<br>

## 7. 결론

<div style="display: flex; justify-content: center; align-items: center;">
  <table style="border-collapse: collapse; text-align: center;">
    <thead>
      <tr>
        <th>도구</th>
        <th>분류</th>
        <th>역할과 사용</th>
      </tr>
    </thead>
    <tbody>
      <tr>
        <td>Homebrew</td>
        <td>시스템 패키지 매니저</td>
        <td>macOS 및 Linux에서 소프트웨어 설치 및 관리</td>
      </tr>
      <tr>
        <td>pip</td>
        <td>Python 패키지 매니저</td>
        <td>프로젝트 또는 전역에 Python 라이브러리 설치</td>
      </tr>
      <tr>
        <td>pipx</td>
        <td>Python CLI 패키지 매니저</td>
        <td>Python CLI 도구를 독립된 가상 환경에 설치</td>
      </tr>
      <tr>
        <td>poetry</td>
        <td>Python 패키지 및 환경관리 도구</td>
        <td>프로젝트별 환경 빌드 및 의존성 관리</td>
      </tr>
      <tr>
        <td>pyenv</td>
        <td>Python 버전 매니저</td>
        <td>다양한 Python 버전을 설치하고 전환</td>
      </tr>
    </tbody>
  </table>
</div>
