---
title: "[MAC] M1 MacBook Pro Environment setting - #1. Homebrew"
description: ""
author: aijinsol
date: 2022-09-01
categories:
  - MAC
tags:
  - homebrew
---

# 1. Install Homebrew

**step1)** [homebrew 홈페이지](https://brew.sh/)에서 `Install Homewbrew` 코드 복사 → MacOS Terminal에서 실행
    
```bash
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
```

<br>

**step2)** 위와 같은 창이 나오면 `Enter` 를 눌러서 `Xcode Command Line Tools` 설치

![fig1](/statics/posts/mac/mac-01-fig1.png){: width="600"}

<br>

**step3)** 설치가 완료되면 나오는 `Next steps:` 하단의 코드 입력
    
```bash
echo 'eval "$(/opt/homebrew/bin/brew shellenv)"' >> /Users/jinsolkim/.zprofile
eval "$(/opt/homebrew/bin/brew shellenv)"
```

![fig2](/statics/posts/mac/mac-01-fig2.png){: width="600"}

**step4)** Terminal 종료 후 재시작

<br>

**step5)** `brew` 명령어 입력 시, 실행된다면 homebrew가 정상적으로 설치된 것!
    
```bash
brew
```
    
![fig3](/statics/posts/mac/mac-01-fig3.png){: width="600"}

<br>

# 2. Install applications with Homebrew

`brew install` 명령어로 필요한 application 설치
    
```bash
brew install --cask visual-studio-code google-chrome 
```

![fig4](/statics/posts/mac/mac-01-fig4.png){: width="600"}
