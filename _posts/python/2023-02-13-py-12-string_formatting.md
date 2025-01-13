---
title: "[Python] String Formatting"
description: ""
author: aijinsol
date: 2023-02-19
categories:
  - Python
tags:
  - formatting
---

# 문자열 formatting

## 1) 문자열 format code 사용하기

### (1) 문자열 format code

| code | description          |
|------|----------------------|
| %s   | 문자열(string)         |
| %c   | 문자 1개(character)    |
| %d   | 정수(integer)          |
| %f   | 부동소수(floating-point)|
| %%   | Literal `%`          |

<br>

### (2) 숫자/문자열 대입

+ 숫자/문자열을 바로 대입하거나 숫자/문자열을 나타내는 변수로 대입!

    ```python
    number = 10
    day = "two"

    print('I ate %d chocolate chip cookies in %s days.' % (number, day))
    ```

    ```bash
    'I ate 10 chocolate chip cookies in two days.'
    ```
    {: file="Output"}

<br>

### (3) `%s`, `%%` 사용

+ `%s` 포맷 코드에서는 어떤 형태의 값이든 변환해 넣을 수 있다. `%s`에 `string / integer / floating-point` 중 어떤 것을 넣더라도 자동으로 `%` 뒤에 있는 값을 문자열로 바꾸기 때문이다. <br>
+ 또한, formatting 연산자와 `%`를 같이 쓸 때는 `%%`를 쓴다.

    ```python
    print('My grade is %s%%.' % 99.25)
    ```

    ```bash
    'My grade is 99.25%.'
    ```
    {: file="Output"}

<br>

## 2) format code와 숫자 함께 사용하기

### (1) 정렬과 공백

+ 예를 들어, `%10s`는 전체 길이가 10개인 문자열 공간에서 대입되는 값을 오른쪽으로 정렬하고, 그 앞의 나머지는 공백으로 채운다.

    ```python
    print("%10s" % 'Hello')
    ```

    ```bash
    '     Hello'
    ```
    {: file="Output"}

<br>

### (2) 소수점 표현하기

+ 아래 예제는 소수점 2번째 자리까지 나타내도록 한 코드다. `.`의 의미는 소수점 포인트를 의미하고, 그 뒤의 숫자 2는 소수점 뒤에 나올 숫자의 개수를 의미한다.

    ```python
    print("%10.2f" % 3.141592)
    ```

    ```bash
    '      3.14'
    ```
    {: file="Output"}

<br>

## 3) format 함수를 사용한 formatting

### (1) 숫자/문자열 바로 대입하기
    
```python
print('I ate {0} grapes and {1} oranges.'.format(10, 'five'))
```

```bash
'I ate 10 grapes and five oranges.'
```

<br>

### (2) 이름으로 넣기

+ `{name}` 형태를 사용하는 경우 format 함수에는 반드시 `name=value`와 같은 형태의 입력값이 있어야 한다.

    ```python
    print('I ate {number} chocolate chip cookies and was happy for {day} days.'.format(number=10, day=5))
    ```

    ```bash
    'I ate 10 chocolate chip cookies and was happy for 5 days.'
    ```
    {: file="Output"}

<br>

### (3) 인덱스와 이름 혼용해서 넣기

+ 인덱스 항목과 `{name}` 형태를 혼용해서 넣는 것도 가능하다.

    ```python
    print('I ate {0} chocolate chip cookies and was happy for {day} days.'.format(10, day=5))
    ```

    ```bash
    'I ate 10 chocolate chip cookies and was happy for 5 days.'
    ```
    {: file="Output"}

<br>

### (4) 정렬

+ 왼쪽 정렬: `:<`  
    
    ```python
    print("{0:<10}".format('hello'))
    ```

    ```bash
    'hello     '
    ```
    {: file="Output"}

<br>

+ 오른쪽 정렬: `:>`
    
    ```python
    print("{0:>10}".format('hello'))
    ```

    ```bash
    '     hello'
    ```
    {: file="Output"}

<br>

+ 가운데 정렬: `:^`
    + 가운데 정렬할 때 공백 갯수가 홀수개(2n+1)이면 앞에 n개 뒤에 (n+1)개를 배치하게 된다.

    ```python
    print("{0:^10}".format('hello'))
    ```

    ```bash
    '  hello   '
    ```
    {: file="Output"}

<br>

### (5) 공백 채우기

+ 정렬문자 `<, >, ^` 바로 앞에 채워 넣을 지정문자를 넣어준다.

    ```python
    print("{0:#^10}".format('hello'))
    ```

    ```bash
    '##hello###'
    ```
    {: file="Output"}

<br>

### (6) 소수점 표현하기
    
```python
x = 3.141592
print("{0:10.4f}".format(x))
```

```bash
'    3.1416'
```
{: file="Output"}

<br>

## 4) f 문자열 formatting

<div class="notice--info" markdown="1">
💡 Python version 3.6부터 f 문자열 formatting 기능 사용 가능
</div>

### (1) f 접두사

+ f 접두사를 이용해 f 문자열 formatting 기능을 사용할 수 있다.
   
    ```python
    name = 'Jinsol Kim'
    age = 100
    print(f'My name is {name}, and I am {age} years old.')
    ```

    ```bash
    'My name is Jinsol Kim, and I am 100 years old.'
    ```
    {: file="Output"}

<br>

### (2) f 문자열 formatting에서 표현식의 사용

+ f 문자열 formatting은 표현식을 지원한다.
    + 표현식이란 문자열 안에서 변수와 `+, -`와 같은 수식을 함께 사용하는 것을 의미.

    ```python
    age = 100
    print(f'I will be {age+1} years old next year.')
    ```

    ```bash
    'I will be 101 years old next year.'
    ```
    {: file="Output"}

<br>

### (3) f 문자열 formatting에서 딕셔너리의 사용

+ 딕셔너리를 사용하여 f 문자열 formatting 표현하기도 가능!
    + `'`와 `"`의 사용 유의하자! 동시에 쓰지 않도록!!
    
    ```python
    d = {'name': 'Jinsol Kim', 'age': 100}
    print(f'My name is {d["name"]}, and I am {d["age"]} years old.')
    print(f"My name is {d['name']}, and I am {d['age']} years old.")
    ```

    ```bash
    'My name is Jinsol Kim, and I am 100 years old.'
    'My name is Jinsol Kim, and I am 100 years old.'
    ```
    {: file="Output"}

<br>

### (4) 정렬

```python
print(f'{"hello":<10}')     # 왼쪽정렬
print(f'{"hello":>10}')     # 오른쪽정렬
print(f'{"hello":^10}')     # 가운데정렬
```

```bash
'hello     '
'     hello'
'  hello   '
```
{: file="Output"}

<br>

### (5) 공백 채우기

```python
f'{"hello":#^10}'
```

```bash
'##hello###'
```
{: file="Output"}

<br>

### (6) 소수점 표현하기

```python
x = 3.141592
print(f'{x:10.4f}')
```

```bash
'    3.1416'
```
{: file="Output"}

<br>

# Reference
+ [점프 투 파이썬 - 문자열 자료형](https://wikidocs.net/13)
