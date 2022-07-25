---
title: "[Python] Module - Practice"
excerpt: ""
categories:
  - Python
tags:
  - module
last_modified_at: 2022-07-26
---

# 1. Directory

```
sub
├── __init__.py
├── sub1
│   ├── __init__.py
│   └── module1.py
└── sub2
    ├── __init__.py
    └── module2.py
```

# 2. Python Files

```python
# sub/__init__.py
```

```python
# sub/sub1/__init__.py

__all__ = ['module1']
```

```python
# sub/sub1/module1.py

import sys
import inspect

def mod1_test1():
	print ("Module1 -> Test1")
	print("Path : ", inspect.getfile(inspect.currentframe()))	# 현재 실행파일의 파일 위치 출력

def mod1_test2():
	print ("Module1 -> Test2")
	print("Path : ", inspect.getfile(inspect.currentframe()))
```
```python
# sub/sub2/__init__.py

__all__ = ['module2']
```

```python
# sub/sub2/module2.py

import sys
import inspect

def mod2_test1():
	print ("Module2 -> Test1")
	print("Path : ", inspect.getfile(inspect.currentframe()))

def mod2_test2():
	print ("Module2 -> Test2")
	print("Path : ", inspect.getfile(inspect.currentframe()))
```

# 3. Examples

## Example 1: `import ~`

```python
import sub.sub1.module1
import sub.sub2.module2

# 사용
sub.sub1.module1.mod1_test1()
sub.sub1.module1.mod1_test2()

sub.sub2.module2.mod2_test1()
sub.sub2.module2.mod2_test2()

--------------------------------------------------
Module1 -> Test1
Path :  /Users/jinsolkim/Desktop/practice/sub/sub1/module1.py
Module1 -> Test2
Path :  /Users/jinsolkim/Desktop/practice/sub/sub1/module1.py
Module2 -> Test1
Path :  /Users/jinsolkim/Desktop/practice/sub/sub2/module2.py
Module2 -> Test2
Path :  /Users/jinsolkim/Desktop/practice/sub/sub2/module2.py
```

## Example 2: `from ~ import ~`

```python
from sub.sub1 import module1
from sub.sub2 import module2 as m2  # alias

module1.mod1_test1()
module1.mod1_test2()

m2.mod2_test1()
m2.mod2_test2()

--------------------------------------------------
Module1 -> Test1
Path :  /Users/jinsolkim/Desktop/practice/sub/sub1/module1.py
Module1 -> Test2
Path :  /Users/jinsolkim/Desktop/practice/sub/sub1/module1.py
Module2 -> Test1
Path :  /Users/jinsolkim/Desktop/practice/sub/sub2/module2.py
Module2 -> Test2
Path :  /Users/jinsolkim/Desktop/practice/sub/sub2/module2.py
```

## Example 3: `from ~ import *`
+ 별표(*)는 웬만하면 쓰지말자
+ 사용 하지도 않을 파일/함수까지 모두 가져와서 파이썬이 실행될때 굳이 불필요한 작업을 할 필요가 없다. 메모리만 잡아먹는다 

```python
from sub.sub1 import *
from sub.sub2 import *

module1.mod1_test1()
module1.mod1_test2()

module2.mod2_test1()
module2.mod2_test2()

--------------------------------------------------
Module1 -> Test1
Path :  /Users/jinsolkim/Desktop/practice/sub/sub1/module1.py
Module1 -> Test2
Path :  /Users/jinsolkim/Desktop/practice/sub/sub1/module1.py
Module2 -> Test1
Path :  /Users/jinsolkim/Desktop/practice/sub/sub2/module2.py
Module2 -> Test2
Path :  /Users/jinsolkim/Desktop/practice/sub/sub2/module2.py
```

<br>

# Reference
+ [인프런 - 프로그래밍 시작하기: 파이썬 입문(Inflearn Original)](https://www.inflearn.com/course/%ED%94%84%EB%A1%9C%EA%B7%B8%EB%9E%98%EB%B0%8D-%ED%8C%8C%EC%9D%B4%EC%8D%AC-%EC%9E%85%EB%AC%B8-%EC%9D%B8%ED%94%84%EB%9F%B0-%EC%98%A4%EB%A6%AC%EC%A7%80%EB%84%90)

<br>
