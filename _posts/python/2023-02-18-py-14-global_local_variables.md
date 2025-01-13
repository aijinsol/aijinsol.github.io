---
title: "[Python] Global & Local variables"
description: ""
author: aijinsol
date: 2023-02-19
categories:
  - Python
tags:
  - global
  - local
  - error
  - shadowing
---

# 1. Global Variable (전역변수)

+ `Global scope`(전역범위)에서 활동하는 변수
+ 즉, 함수를 포함하여 script 전체에서 모든 요소가 해당 변수에 접근할 수 있다.

<br>

# 2. Local Variable (지역변수)

+ `Local scope`(지역 범위)에서만 활동하는 변수
+ 함수 안에서 선언된 변수라면 해당 변수는 해당 함수 안에서만 사용될 수 있다.

<br>

# 3. `global` 키워드
+ 함수 안에서 선언된 지역변수라도 `global` 키워드를 사용하여 `global scope`에서도 호출 가능하게 해줄 수 있다.
+ `global` 키워드를 통해 전역변수를 함수 내에서 참조하는 것을 뛰어넘어 값을 할당해준다.
+ 즉, `global` 키워드를 사용하면 scope의 경계가 무너진다고 생각하면 된다! ← 이렇기 때문에 개발 시 `global` 키워드를 사용하지 않는 것이 좋다. 주의!! ⭐️

<br>

![image01](/assets/images/2023-02-18-global_local_variable-01.jpg){: .align-center}{: width="50%" height="50%"}

<br>

# 4. 예시로 이해하자!

## Example 1) Global Variable

전역변수는 함수 내부를 포함해서 script 전체에서 접근 가능하다.

```python
def func():
    a = 'local variable'
    print(a)
    print(b)
    
b = 'global variable'

func()
```

```bash
local variable
global variable
```
{: file="Output"}

+ 변수 `b`는 `global scope`에 선언되어 있으므로 함수 `func()` 내부에서 호출 가능하다.

<br>

## Example 2) Local Variable

함수 내에서 선언된 지역변수는 함수 내에서만 사용 가능하다.

```python
def func():
    a = 'local variable'
    print(a)
    
func()
```

```bash
local variable
```
{: file="Output"}

<br>

```python
def func():
    a = 'local variable'
    print(a)

print(a)
```

```bash
---------------------------------------------------------------------------
NameError                                 Traceback (most recent call last)
Cell In [1], line 6
      3     print(a)
      5 func()
----> 6 print(a)

NameError: name 'a' is not defined
```
{: file="Output"}

+ 함수 밖에서 함수 내에 선언된 지역변수에 접근하려 하면 `NameError` 발생!
    + 함수 내부에서 선언된 지역변수는 함수의 호출이 끝남과 동시에 소멸되기 때문에, 함수 밖에서 접근하려 하면 선언되지 않은 변수에 접근하는 것으로 인식되어서 에러가 발생하게 되는 것

<br>

## Example 3) `global` keyword

`Local scope`에서 선언된 지역변수를 `global`로 지정해주면 `global scope`에서도 호출이 가능하다. 

```python
def func():
    global a
    a = 'local variable'
    print(a)
    
func()
print(a)
```

```bash
local variable
local variable
```
{: file="Output"}

<br>

## Example 4) `global` keyword

함수 밖에서 선언된 전역변수를 함수 내부에서 값을 참조하는 것을 뛰어넘어 값을 할당해 변경하려 하면 에러가 뜬다. 이럴 때는 `global` 키워드를 통해 해결할 수 있다.

```python
a = 100
def func():
    a += 1

func()
```

```bash
---------------------------------------------------------------------------
UnboundLocalError                         Traceback (most recent call last)
Cell In [12], line 1
----> 1 func()

Cell In [11], line 3, in func()
      2 def func():
----> 3     a += 1

UnboundLocalError: local variable 'a' referenced before assignment
```
{: file="Output"}

+ `UnboundLocalError`가 발생하는 이유?
    + 함수 밖에서 생성된 변수가 함수 내에서 참조(referenced)만 된다면 global variable
    + 그런데 이 변수가 함수 안에서 값을 받게 되면 `global`로 따로 선언해주지 않는 한, 자동으로 local variable로 변경된다.
    + 따라서, 함수에서 참조만 받을 때는 에러가 나지 않다가 값을 부여받는 순간 `UnboundLocalError`가 발생하게 된다. (`func()` 함수 내에서 변수 `a`가 선언된 적이 없으므로 `+=`와 같은 수식 진행 불가)

```python
a = 100
def func():
    global a
    a += 1

func()
print(a)
```

```bash
101
```
{: file="Output"}

+ 결론!!
    + 함수 밖에서 생성된 전역변수를
        + 함수 내에서 <b>참조</b> 시 → Global Variable
        + 함수 내에서 <b>값 할당</b> 시 → Local Variable

<br>

## Example 5)

만약 함수 밖에서 선언된 전역변수와 함수 내에서 선언된 지역변수의 이름이 같은 경우에 `global` 키워드를 사용하지 않으면 서로 전혀 다른 별개의 변수로 취급된다.
+ Local scope에서 선언된 변수 이름이 global scope에서의 변수 이름과 중첩되면 local variable이 우선적으로 참조되고, global variable은 접근이 불가하다.
    + `Shadowing` 효과: 파이썬은 변수나 함수를 찾을 때 다음 순서의 scope 내에서 찾는다.
        + `local scope` → `global scope`
        + 좁은 범위에서 시작해서 넓은 범위로 나아가므로, 만약 동일한 이름의 변수들이 서로 다른 scope 안에서 선언 된다면 더 좁은 범위에 있는 변수를 가리키게 되는 shadowing 효과가 나타난다.

```python
def func():
    a = 'local variable'
    print(a)

a = 'global variable'

print(a)
func()
print(a)
```

```bash
global variable
local variable
global variable
```
{: file="Output"}

<br>

```python
def func():
    global a
    a = 'local variable'
    print(a)

a = 'global variable'

print(a)
func()
print(a)
```

```bash
global variable
local variable
local variable
```
{: file="Output"}

<br>

# Reference

+ [긴기린그림과 데이터 - UnboundLocalError](https://gin-girin-grim.tistory.com/2)
+ [shoot for the moon! - Scope](https://velog.io/@mjhuh263/TIL-45-Python-Scope)
