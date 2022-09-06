---
title: "[MAC] M1 MacBook Pro Environment setting - #1. Homebrew"
excerpt: ""
categories:
 - MAC
tags:
  - homebrew
last_modified_at: 2022-09-01
---

# 1. Install Homebrew

**step1)** [homebrew 홈페이지](https://brew.sh/)에서 `Install Homewbrew` 코드 복사 → MacOS Terminal에서 실행
    
```bash
$ /bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
```

<br>

**step2)** 위와 같은 창이 나오면 `Enter` 를 눌러서 `Xcode Command Line Tools` 설치

![image01](/assets/images/2022-09-01-homebrew_01.png){: .align-center}{: width="80%" height="80%"}

<br>

**step3)** 설치가 완료되면 나오는 `Next steps:` 하단의 코드 입력
    
```bash
$ echo 'eval "$(/opt/homebrew/bin/brew shellenv)"' >> /Users/jinsolkim/.zprofile
$ eval "$(/opt/homebrew/bin/brew shellenv)"
```

![image02](/assets/images/2022-09-01-homebrew_02.png){: .align-center}{: width="80%" height="80%"}

**step4)** Terminal 종료 후 재시작

<br>

**step5)** `brew` 명령어 입력 시, 실행된다면 homebrew가 정상적으로 설치된 것!
    
```bash
$ brew
```
    
![image03](/assets/images/2022-09-01-homebrew_03.png){: .align-center}{: width="80%" height="80%"}

<br>

# 2. Install applications with Homebrew

`brew install` 명령어로 필요한 application 설치
    
```bash
$ brew install --cask visual-studio-code google-chrome 
```

![image04](/assets/images/2022-09-01-homebrew_04.png){: .align-center}{: width="80%" height="80%"}
