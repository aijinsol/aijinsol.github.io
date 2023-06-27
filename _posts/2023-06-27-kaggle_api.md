---
title: "[ETC] Kaggle API 사용법"
excerpt: ""
categories:
  - ETC
tags:
  - Kaggle
last_modified_at: 2023-06-27
---


> Kaggle API를 활용하여 <br>
> + Kaggle의 dataset을 원하는 폴더에 직접 다운로드 받아보자.
> + Submission 파일을 Kaggle에 업로드해보자.

# 1. Kaggle API 설치

```bash
pip install kaggle --upgrade
```

<br>

# 2. Kaggle API Token 다운로드 (`.json` 파일)
+ Step 1) [Kaggle.com](https://www.kaggle.com/) 로그인
+ Step 2) Settings > API > Create New Token 클릭! (`kaggle.json` 파일 다운로드)

![image01](/assets/images/2023-06-27-kaggle_api-1.png){: .align-center}{: width="80%" height="80%"}

<br>

# 3. `kaggle.json` 파일 이동 및 권한 설정
+ (Mac OS 기준) `~/.kaggle/kaggle.json` 위치로 이동
+ 보안을 위하여 다음과 같이 권한을 변경해준다
```bash
chmod 600 ~/.kaggle/kaggle.json
```

<br>

# 4. Kaggle API를 활용한 Dataset 다운로드
예시로, Titanic Competition의 dataset을 다운받아보자.
+ Step 1) [Titanic Competition](https://www.kaggle.com/competitions/titanic)의 Data 탭으로 이동
+ Step 2) 하단에 작성되어 있는 Kaggle API로 데이터를 받을 수 있는 command line 복사
    + `kaggle competitions download -c titanic`
+ Step 3) 터미널에서 해당 명령어 실행

<br>

# 5. Kaggle API를 활용한 결과 제출하기
+ Step 1) [Titanic Competition](https://www.kaggle.com/competitions/titanic)의 Submission Predictions 탭으로 이동
+ Step 2) 하단에 작성되어 있는 Kaggle API로 파일을 제출할 수 있는 command line 복사
    + `kaggle competitions submit -c titanic -f submission.csv -m "Message"`
    + `-f` 옵션: 제출할 `.csv file` 이름 작성
    + `-m` 옵션: 원하는 `message` 작성
+ Step 3) 터미널에서 해당 명령어 실행

`Submissions` 탭을 refresh하면 내가 제출한 파일이 업로드되어있는 것을 확인할 수 있다.
