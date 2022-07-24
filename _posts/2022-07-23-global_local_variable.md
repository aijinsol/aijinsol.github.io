---
title: "[Python] Global Variable, Local Variable"
excerpt: ""
categories:
  - python
tags:
  - global variable
  - local variable
last_modified_at: 2022-07-23
---

> [연오의 파이썬 - 전역변수와 지역변수](https://python.bakyeono.net/chapter-3-4.html)를 보고 기억할 내용을 제 언어로 요약정리한 포스팅이라 표현 및 설명이 매끄럽지 않을 수 있습니다!

# 1. 전역변수(global variable)
+ 함수 밖에서 변수를 정의하면 전역변수
+ 프로그램 어디서든 부를 수 있는 변수
+ 전역변수는 어디에서나 읽을 수 있지만, 함수 안에서 전역변수에 새로운 값 대입하는 것은 금지됨 (`global`문을 사용하면 예외적으로 가능)

<br>

# 2. 지역변수(local variable)
+ 함수 속에서 작성한 변수
+ 함수가 실행되는 동안에만 존재
+ 각 함수가 호출되어 실행될 때 만들어지고, 함수의 실행이 끝나면 삭제된다
+ 지역변수를 이용하면 그 데이터와 관련된 문제를 함수 내부의 문제로 국한시킬 수 있다

<br>

# 3. 전역변수/지역변수 해석 및 비교

```python
seconds_per_minute = 60

def minutes_to_seconds(minutes):
  seconds = minutes * seconds_per_minute
  return seconds

print(minutes_to_seconds(3))
print(seconds)

------------------------------
180
NameError: name 'seconds' is not defined
```

+ <b>코드 해석</b>
  + 전역변수: `seconds_per_minute`
  + 지역변수: `minutes`, `seconds`
  + 에러 발생 이유: `seconds`라는 지역변수를 함수 밖에서 읽으려했기 때문. 해당 변수는 함수 밖에는 존재하지 않는다

<br>

Table 1: 함수 안밖에서의 전역변수와 지역변수

|    특징     | 전역변수  | 지역변수 |
|:---------:|:-----:|:----:|
| 함수 안에서 읽기 |  가능   |  가능  |
| 함수 안에서 수정 | 불가(*) |  가능  |
| 함수 밖에서 읽기 |  가능   |  불가  |
| 함수 밖에서 수정 |  가능   |  불가  |

<br>

# 4. 함수 안에서 전역변수 수정하기 - `global`

<div class="notice--info" markdown="1">
💡 함수 안에서 전역변수를 수정할 수 없지만, 꼭 필요하다면 `global`문을 이용해 이 규칙을 어길 수 있다.
</div>

## 1) 함수의 실행 결과 누적하기
+ 함수가 전역변수를 수정해야하는 대표적인 상황
  + 함수의 실행 결과를 누적해야 하는 경우

```python
num_stamp = 0   # 쿠폰 스탬프가 찍힌 횟수 (전역변수)

def stamp():
  num_stamp = num_stamp + 1
  print(num_stamp)

stamp()

------------------------------
UnboundLocalError: local variable 'num_stamp' referenced before assignment
```

+ <b>코드 해석</b>
  + 에러 발생 이유
    + 함수 안에서 `num_stamp`에 `num_stamp + 1`을 대입하려했는데 `num_stamp`이 아직 만들어지지 않은 지역변수이기 때문
    + 원래 의도는 전역변수 `num_stamp`를 불러와 읽으려했겠지만, 함수 안에서의 `num_stamp`는 지역변수로 해석된다
    + 이 문제를 해결하고자 한다면 `global`문을 이용해 `num_stamp` 변수가 전역변수임을 명시적으로 밝혀야 한다

## 2) `global`문
+ `global`문을 사용하면 함수 안에서도 전역변수 값 수정 가능
+ 함수 안에서 `global ${Variable}`을 실행하면 해당 변수가 전역변수임을 밝히게 되면서 전역변수의 값을 수정할 수 있게 된다

```python
num_stamp = 0   # 전역변수

def stamp():
  global num_stamp    # num_stamp가 전역변수임을 밝힘
  num_stamp = num_stamp + 1   # 이제 num_stamp 수정 가능
  print(num_stamp)

stamp()   # 1
stamp()   # 2
```

## 3) `global`문은 되도록 사용하지 않도록 하자!
+ `global`문을 사용하면 함수가 매개변수I(parameter)와 반환값(return value)을 이용해 외부와 소통하는 자연스러운 흐름을 깨트리는 일
+ 함수 안에서 전역변수를 수정하지 않고, 매개변수와 반환값만 이용하더라도 함수의 실행결과를 누적하는 데 부족함이 없다
+ 다른 사람의 프로그램을 읽기 위해 global 사용법을 알아두되, 가급적 사용하지 말자!

```python
num_stamp = 0   # 전역변수

def stamp(num_stamp):   # 지역변수이면서 매개변수. (위 전역변수와 전혀 다른 변수!!)
  num_stamp = num_stamp + 1
  print(num_stamp)
  return num_stamp

# 전역변수에 함수의 반환값 대입
num_stamp = stamp(num_stamp)    # 1
num_stamp = stamp(num_stamp)    # 2
```

+ <b>코드 해석</b>
  + stamp() 함수는 자신의 지역변수인 num_stamp만 수정하고, 전역변수 num_stamp는 수정하지 않는다
  + 전역변수 num_stamp는 함수 밖에서만 수정되고 있다

<br>

# Reference
+ [연오의 파이썬 - 전역변수와 지역변수](https://python.bakyeono.net/chapter-3-4.html)

<br>
