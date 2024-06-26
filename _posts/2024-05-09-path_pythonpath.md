---
title: "[ETC] Path vs PythonPath"
excerpt: "환경변수"
categories:
  - ETC
tags:
  - path
  - pythonpath
last_modified_at: 2024-05-09
---

# 1. PATH 환경변수

## 1) 정의

운영체제가 프로그램의 실행 파일을 찾기 위해 참조하는 디렉토리 경로 목록을 저장하는 환경변수

## 2) 기능

- <b>명령 실행</b>: 사용자가 명령 프롬프트나 쉘에서 프로그램을 실행하려 할 때, 시스템은 `PATH`에 지정된 디렉토리들을 검색해 실행 파일을 찾는다.
- <b>경로 구분</b>: `PATH` 내의 다양한 디렉토리 경로들은 콜론(:)을 사용하여 구분된다.
- <b>`bin` 폴더</b>: 대부분의 UNIX 및 UNIX 계열 시스템에서 `bin` 폴더는 시스템 또는 사용자의 실행 파일을 포함하며, 이 폴더 경로들은 일반적으로 PATH에 포함된다.
- <b>동작 원리</b>: 사용자가 터미널에서 명령어를 실행할 때, 시스템은 `PATH`에 명시된 순서대로 디렉토리를 탐색하여 해당 명령어의 실행 파일을 찾는다.

<br>

# 2. PYTHONPATH 환경변수

## 1) 정의

파이썬 인터프리터가 표준 라이브러리 위치 외에 추가적인 파이썬 모듈과 패키지(라이브러리)를 찾기 위해 참조하는 디렉토리 경로 목록을 저장하는 환경변수

## 2) 기능

- <b>모듈과 패키지 탐색</b>: 파이썬 스크립트 실행 시, `PYTHONPATH`에 지정된 경로들을 검색하여 필요한 파이썬 모듈과 패키지를 찾는다.
- <b>검색 경로</b>: `PYTHONPATH`는 파이썬 인터프리터가 모듈과 패키지를 찾을 수 있는 추가적인 경로를 제공한다. 이는 표준 라이브러리 디렉토리 외부에 있는 리소스를 인식하게 해준다.
- <b>환경 구성</b>: 개발자는 특정 프로젝트에 필요한 외부 모듈과 패키지가 위치한 디렉토리를 `PYTHONPATH`에 추가하여 프로젝트별 독립적인 파이썬 환경을 구성할 수 있다.
