---
title: "[Python] Matplotlib Font Settings"
description: ""
author: aijinsol
date: 2022-06-18
categories:
 - Python
tags:
  - Matplotlib
---

# `Matplotlib` 한글 폰트 설정하기

> `Matplotlib`을 이용해 시각화하는 경우, 한글폰트가 깨져 나오는 경우가 있다.  
이는 `Matplotlib`에 한글폰트가 설정되지 않았기 때문이다.  
한글폰트를 설정하기 위한 3가지 방식이 있다.
> 1. `FontProperties` 사용
> 2. `matplotlib.rcParams[]`으로 전역글꼴 설정
> 3. `rcParams`를 설정파일에 직접 설정 (`Matplotlib`의 default font를 한글 font로 설정하여 사용)

<br>

## 1. `FontProperties` 사용

+ 적용할 항목의 텍스트에 바로 폰트를 지정해주는 방식
  + 아래의 항목에 설정할 수 있다.
    + `matplotlib.pyplot.title()`
    + `matplotlib.pyplot.xlabel()`
    + `matplotlib.pyplot.ylabel()`
    + `matplotlib.pyplot.legend()`
    + `matplotlib.pyplot.text()`
    + `matplotlib.axes.set_title()`

```python
import matplotlib.pyplot as plt


path = '/Users/mljinsol/Library/Fonts/NanumGothic.otf'
fontprop = fm.FontProperties(fname=path, size=20)

plt.plot(data)
plt.title('MyTitle', fontproperties=fontprop)
plt.xlabel('xlabel', fontproperties=fontprop)
plt.ylabel('ylabel', fontproperties=fontprop)
plt.show()
```

<br>

## 2. `matplotlib.rcParams[]`으로 전역글꼴 설정

+ 그래프에 바로 폰트를 설정하여 해당 그래프의 모든 텍스트에 (동일한) 폰트를 한 번에 적용하는 방식
+ 위의 1번과 다른 점은 `fontproperties=fontprop` 을 일일히 해주지 않아도 된다.

```python
plt.rcParams['font.family'] = 'NanumGothicOTF'  # font.family: 설정되어 있는 폰트 글꼴
plt.rcParams['font.size'] = 20  # font.size: 설정되어 있는 폰트 사이즈

plt.plot(data)
plt.title('MyTitle')
plt.xlabel('xlabel')
plt.ylabel('ylabel')
plt.show()
```

+ `rcParams` 대신 `FrontProperties`와 `plt.rc`를 사용하는 방법도 있다.

```python
path = '/Users/mljinsol/Library/Fonts/NanumGothic.otf'
font_name = fm.FontProperties(fname=path, size=20).get_name()  # NanumGothic
plt.rc('font', family=font_name)

plt.plot(data)
plt.title('MyTitle')
plt.xlabel('xlabel')
plt.ylabel('ylabel')
plt.show()
```

<br>

## 3. `rcParams`를 설정파일에 직접 설정

+ `mpl.matplotlib_fname()`을 통해 폰트설정파일 경로 확인 후, 해당 설정파일에 직접 폰트를 지정하는 방식. matplotlib의 설정파일에 폰트를 직접 설정하는 것이므로 컴퓨터를 껐다켜도 해당 폰트가 default로 사용된다. 

```python
print(mpl.matplotlib_fname())
```

```bash
/Users/mljinsol/.pyenv/versions/3.10.0/lib/python3.10/site-packages/matplotlib/mpl-data/matplotlibrc
```
{: file="Output"}

+ 해당 설정파일을 열어 `font.family`를 원하는 폰트로 설정해준다.

```bash
vi /Users/mljinsol/.pyenv/versions/3.10.0/lib/python3.10/site-packages/matplotlib/mpl-data/matplotlibrc
```

```
font.family : NanumGothicOTF
```

+ 이렇게 설정해두면 따로 `matplotlib` 사용할 때마다 한글폰트 설정을 해주는 번거로움이 없다.

```python
plt.plot(data)
plt.title('MyTitle')
plt.xlabel('xlabel')
plt.ylabel('ylabel')
plt.show()
```

<br>

# ++ 추가로 함께 알아두어야 할 코드들!!

## 1. 설치된 `Matplotlib`의 버전과 위치정보 확인

```python
print(mpl.__version)        # mpl version
print(mpl.__file__)         # mpl installed directory
print(mpl.get_configdir())  # mpl config directory
print(mpl.get_cachedir())   # mpl cache directory
```

+ 새로운 폰트를 설치 후 불러오지 못할 때 해결방법
  + 만약 새로운 폰트를 설치했는데 해당 폰트를 불러오지 못한다면, 터미널에서 `mpl.get_cachedir()`로 나타나는 캐시위치로 이동해서 캐시파일을 확인한다.
  + 캐시파일에 새로운 폰트가 나타나지 않는다면 캐시파일을 삭제 후 파이썬 파일을 다시 실행하면 캐시파일에 새로운 폰트가 저장된 것을 확인할 수 있다.

<br>

## 2. 설치된 폰트 확인

+ 특정 확장자(ex. ttf)의 폰트를 확인하고 싶은 경우

```python
font_list = fm.findSystemFonts(fontpaths=None, fontext='ttf')
```

+ 모든 폰트를 확인하고 싶은 경우

```python
[f.name for f in fm.fontManager.ttflist]

# output
['STIXSizeOneSym',
 'cmr10',
 ...]
```

+ `Nanum` 이름이 들어간 폰트이름과 폰트경로 확인하기

```python
[(f.name, f.fname) for f in fm.fontManager.ttflist if 'Nanum' in f.name]

# output
[('NanumGothicOTF', '/Users/mljinsol/Library/Fonts/NanumGothicBold.otf'),
 ('NanumGothicOTF', '/Users/mljinsol/Library/Fonts/NanumGothic.otf'),
 ...
 ('NanumBarunpen', '/Users/mljinsol/Library/Fonts/NanumBarunpenB.ttf')]
```

<br>

## 3. 그래프에서 마이너스 폰트가 깨지는 문제 해결 코드

```python
mpl.rcParams['axex.unicode_minus'] = False
```

<br>

# Reference
+ [인프런 - 공공데이터로 파이썬 데이터 분석 시작하기](https://www.inflearn.com/course/%EA%B3%B5%EA%B3%B5%EB%8D%B0%EC%9D%B4%ED%84%B0%EB%A1%9C-%ED%8C%8C%EC%9D%B4%EC%8D%AC-%EB%8D%B0%EC%9D%B4%ED%84%B0-%EB%B6%84%EC%84%9D-%EC%8B%9C%EC%9E%91%ED%95%98%EA%B8%B0/lecture/17721?tab=curriculum)
