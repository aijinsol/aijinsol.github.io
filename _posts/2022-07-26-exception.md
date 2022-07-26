---
title: "[Python] Error/Exception"
excerpt: ""
categories:
  - Python
tags:

  - exception
  - inflearn
last_modified_at: 2022-07-26
---
> 강의정보 ([인프런 - 프로그래밍 시작하기: 파이썬 입문(Inflearn Original)](https://www.inflearn.com/course/%ED%94%84%EB%A1%9C%EA%B7%B8%EB%9E%98%EB%B0%8D-%ED%8C%8C%EC%9D%B4%EC%8D%AC-%EC%9E%85%EB%AC%B8-%EC%9D%B8%ED%94%84%EB%9F%B0-%EC%98%A4%EB%A6%AC%EC%A7%80%EB%84%90))
> + Chapter07-1
> + Python Exception

# 1. Error/Exception (오류/예외)

<div class="notice--info" markdown="1">
💡 본 포스팅에서는 편의상 오류와 예외를 구분하지 않고 '오류'로 통일하여 기재한다.
</div>

## 1) 오류
+ 생각한 의도대로 프로그램이 동작하지 않고, 작성한 코드에서 비정상적인 이벤트가 발생하는 것
+ 예측이 가능한 경우 / 예측이 불가능한 경우(하드디스크 용량 부족, 메모리 부족 등 OS에서 발생하는 오류)

## 2) 오류 종류
+ NameError
+ ZeroDivisionError
+ IndexError
+ KeyError
+ AttributeError
+ ValueError
+ FileNotFoundError
+ TypeError
+ ...

## 3) 오류 처리
+ 문법적으로는 오류가 없지만, 코드 실행 프로세스(단계)에서 발생하는 오류도 중요
+ 오류는 반드시 처리한다
+ 로그를 반드시 남긴다 (개발할 때 로그는 증거!)
+ 오류 무시 활용

<br>

# 2. 오류 종류

## 1) SyntaxError
+ 문법 오류

```python
print('error)

------------------------------
SyntaxError: EOL while scanning string literal
```

```python
print('error'))

------------------------------
SyntaxError: unmatched ')'
```

```python
if True
    pass

------------------------------
SyntaxError: invalid syntax
```

## 2) NameError
+ 지역변수, 전역변수를 찾을 수 없는 경우 발생 (참조 없음)

```python
a = 1
b = 2
print(c)

------------------------------
NameError: name 'c' is not defined
```

## 3) ZeroDivisionError
+ 숫자를 0으로 나누려는 경우 발생

```python
print(100 / 0)

------------------------------
ZeroDivisionError: division by zero
```

## 4) IndexError
+ 없는 인덱스에 접근하려 할 때 발생

```python
x = [50, 60, 70]

print(x[1])
print(x[10])

------------------------------
IndexError: list index out of range
```

```python
x = [50, 60, 70]

print(x.pop())
print(x.pop())
print(x.pop())
print(x.pop())

------------------------------
IndexError: pop from empty list
```

## 5) KeyError
+ 없는 key 값에 접근하려 할 때 발생
+ 주로 dictionary 사용시 발생
+ get() Method를 쓰는 것이 안전
  + get() Method는 오류를 발생시키지 않고, key 값이 없으면 `None` 출력

```python
dic = {'name': 'Kim', 'Age': 10, 'City': 'Seoul'}
print(dic['hobby'])

------------------------------
KeyError: 'hobby'
```

```python
dic = {'name': 'Kim', 'Age': 10, 'City': 'Seoul'}
print(dic.get('hobby'))

------------------------------
None
```

## 6) AttributeError
+ 모듈, 클래스에 있는 잘못된 속성 사용시 발생

```python
import time
print(time.time2())

------------------------------
AttributeError: module 'time' has no attribute 'time2'
```

## 7) ValueError
+ 부적절한 값을 인자로 받았을 때 발생
+ 리스트에서 없는 값에 접근하려 할 때 발생 (참조 없음)

```python
print(int('seoul'))

------------------------------
ValueError: invalid literal for int() with base 10: 'seoul'
```

```python
x = [10, 20, 30]
x.remove(200)

------------------------------
ValueError: list.remove(x): x not in list
```

## 8) FileNotFoundError
+ 존재하지 않는 파일이나 디렉토리에 접근하려 할 때 발생

```python
f = open('test.txt')

------------------------------
FileNotFoundError: [Errno 2] No such file or directory: 'test.txt'
```

## 9) TypeError
+ 서로 다른 type으로 연산을 수행하려 할 경우 발생

```python
x = [1,2]
y = (1,2)
z = 'test'

print(x + y)  # TypeError: can only concatenate list (not "tuple") to list
print(x + z)  # TypeError: can only concatenate list (not "str") to list
print(y + z)  # TypeError: can only concatenate tuple (not "str") to tuple
```

<br>

# 2. 오류 처리
+ `try`: 에러가 발생할 가능성이 있는 코드 실행
+ `except ${Error} [as ${Error Variable}]`
+ `else`: try 블록의 에러가 없을 경우 실행
+ `finally`: 항상 실행

## 1) `try, except`문
+ try 블록 수행 중 오류 발생시 except 블록 수행 O
+ try 블록에서 오류가 발생하지 않는다면 except 블록 수행 X

```python
try:
    ...
except [${Error} [as ${Error Variable}]]:
    ...
```
+ (참고) []기호는 괄호 안의 내용을 생략할 수 있다는 관례 표기법

### (1) `try, except`만 쓰는 방법
+ 오류 종류에 상관없이 오류 발생시 except 블록 실행
+ 어떤 오류가 발생할지 모를 때 사용하기도 한다. 모든 오류 처리 가능

```python
try:
    ...
except [Exception]:
    ...
```
### (2) 발생한 오류만 포함한 except문
+ 오류가 발생했을 때 except문에 미리 정해 놓은 오류 이름과 일치할 때만 except 블록 수행

```python
try:
    ...
except ${Occured Error}:
    ...
```

### (3) 발생 오류와 오류 메시지 변수까지 포함한 except문
+ 위 (2)에서 오류 메시지의 내용까지 알고 싶을 때 사용하는 방법

```python
try:
    ...
except ${Occured Error} as ${Error Variable}:
    ...
```

example)

```python
try:
    5 / 0
except ZeroDivisionError as e:
    print(e)

------------------------------
division by zero
```

<br>

## 2) `try, finally`문
+ try문에 finally절 사용 가능
+ finally절
  + try문 수행 도중 오류 발생 여부에 상관없이 항상 수행된다
  + 보통 사용한 리소스를 close해야 할 때에 많이 사용된다

```python
f = open('text.txt', 'w')
try:
    ...
finally:
    f.close()
```
+ text.txt 파일을 쓰기 모드로 열기 → try문 수행 → 오류 발생 여부와 상관없이 finally절에서 `f.close()`로 열린 파일 닫기 가능

<br>

## 3) `try, else`문
+ try문 수행 중
  + 오류 발생 O → except절 수행
  + 오류 발생 X → else절 수행

```python
try:
    height = int(input('Input your height: '))
except:
    print('Input not correct')  # 숫자가 아닌 다른 값 입력 시 본 오류 발생
else:                         # 오류가 없을 경우 else절 수행
    if height >= 100:
        print('You are allowed to ride the roller-coaster.')
    else:
        print('Please ride with adults.')
```




<br>

## 4) 여러개의 오류 처리
+ 만약 Error1이 발생했다면, 먼저 발생한 오류인 Error1에 대한 에러 메시지만 발생
+ 2개 이상의 오류를 함께 처리하기 위해서는 괄호를 사용하여 함께 묶어 처리

```python
try:
    ...
except ${Error1} [as ${Error1 Variable}]:
    ...
except ${Error2} [as ${Error2 Variable}]:
    ...
```

```python
try:
    ...
except ${Error1, Error2} [as ${Error Variable}]:
    ...
```

example)

```python
try:
    a = [1,2]
    print(a[3])
    5/0
except ZeroDivisionError as e:
    print(e)
except IndexError as e:
    print(e)

------------------------------
list index out of range
```

```python
try:
    a = [1,2]
    print(a[3])
    5/0
except (ZeroDivisionError, IndexError) as e:
    print(e)

------------------------------
list index out of range
```

<br>

# 3. 오류 무시하기
+ 특정 오류 발생시 그냥 통과시켜야 하는 경우

```python
try:
    f = open("no_exsisting_file.txt", 'r')
except FileNotFoundError:
    pass
```

<br>

# 4. 오류 일부러 발생시키기
+ 오류를 일부러 발생시켜야하는 경우, `raise` 명령어를 사용해 오류를 강제로 발생시킨다

example)
+ Person 클래스를 상속받는 자식 클래스는 반드시 eat 함수를 구현하도록 만들고 싶은 경우 또는 강제로 그렇게 하고 싶은 경우
+ 그런데 자식 클래스가 fly 함수를 구현하지 않은 상태로 eat 함수를 호출한다면 오류를 발생시키도록 한다

```python
class Person:
    def eat(self):
        raise NotImplementedError

class Girl(Person):
    pass

jinsol = Girl()
jinsol.eat()

------------------------------
NotImplementedError
```

+ `NotImplementedError`가 발생되지 않게 하기 위해 Girl 클래스에 eat 함수를 반드시 구현해야 한다
+ 상속받는 클래스에서 함수를 재구현하는 것을 `method overriding`이라고 부른다

```python
class Girl(Person):
    def eat(self):
        print('delicious!')

jinsol = Girl()
jinsol.eat()

------------------------------
delicious!
```

<br>

# 5. 직접 예외 만들기
+ 특수한 경우의 예외 처리를 위해 예외를 직접 만들어 사용

```python
class MyError(Exception):
    pass

def deserts(snacks):
    if snacks == 'carrot':
        raise MyError()
    print(snacks)

deserts('chocolate')  # chocolate
deserts('carrot')     # __main__.MyError
```

+ 예외 처리 기법을 이용하여 MyError 발생을 예외 처리해 보자!

```python
try:
    deserts('chocolate')
    deserts('carrot')
except MyError:
    print('Not for my desert!!')

------------------------------
chocolate
Not for my desert!!
```

+ 오류 메시지를 출력하고 싶다면 아래와 같이 오류 변수 활용!

```python
try:
    deserts('chocolate')
    deserts('carrot')
except MyError as e:
    print(e)
------------------------------
chocolate
```

+ 하지만 `print(e)`로 바로 출력하려고 하면 오류 메시지가 출력되지 않는다
+ 오류 메시지 출력시 오류 메시지가 보이도록 하기 위해서는 오류 클래스에 `__str__` method를 구현해야 한다
  + `__str__` method: 오류 메시지를 print문으로 출력할 경우 호출되는 method

```python
class MyError(Exception):
    def __str__(self):
        return 'Not for my desert!!'

def deserts(snacks):
    if snacks == 'carrot':
        raise MyError()
    print(snacks)

try:
    deserts('chocolate')
    deserts('carrot')
except MyError as e:
    print(e)
------------------------------
chocolate
Not for my desert!!
```

<br>

# 6. Example

```python
name = ['Kim', 'Lee', 'Park']

try:
    x = 'Cho'
    y = name.index(x)
    print('{} Found! #{} in name list'.format(x, y + 1))
except Exception:
    print('Not found! - Occurred ValueError!')
else:
    print('Ok! - else.')
finally:
    print('Done!')

--------------------------------------------------
Not found! - Occurred ValueError!
Done!
```

```python
# Kim이 아닌 사람이 들어오면 예외가 발생하도록 처리해보자.
try:
    a = 'Kim'
    if a == 'Kim':
        print('OK, pass!')
    else:
        raise ValueError
except ValueError:
    print('Exception Occurred!')
else:
    print('OK! - else.')

--------------------------------------------------
OK, pass!
OK! - else.
```

```python
try:
    a = 'Park'
    if a == 'Kim':
        print('OK, pass!')
    else:
        raise ValueError
except ValueError:
    print('Exception Occurred!')
else:
    print('OK! - else.')

--------------------------------------------------
Exception Occurred!
```

# Reference
+ [인프런 - 프로그래밍 시작하기: 파이썬 입문(Inflearn Original)](https://www.inflearn.com/course/%ED%94%84%EB%A1%9C%EA%B7%B8%EB%9E%98%EB%B0%8D-%ED%8C%8C%EC%9D%B4%EC%8D%AC-%EC%9E%85%EB%AC%B8-%EC%9D%B8%ED%94%84%EB%9F%B0-%EC%98%A4%EB%A6%AC%EC%A7%80%EB%84%90)
+ [점프 투 파이썬 - 예외 처리](https://wikidocs.net/30#_3)

<br>
