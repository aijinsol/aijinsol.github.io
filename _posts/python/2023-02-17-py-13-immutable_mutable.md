---
title: "[Python] Immutable vs. Mutable"
description: ""
author: aijinsol
date: 2023-02-17
categories:
  - Python
tags:
  - immutable
  - mutable  
---

> <b>"파이썬은 객체로 동작한다!"</b>는 사실을 명심하고 아래 내용을 접근하자.

# 1. Immutable vs. Mutable

파이썬의 객체(object)는 그 속성이 immutable 또는 mutable한 객체로 구분된다.

## 1) immutable

+ 객체 종류: int, float, str, tuple, bool
+ 값이 변하지 않음
+ 값이 같은 경우에 변수에 상관없이 동일한 주소 참조

## 2) mutable

+ 객체 종류: list, dict, set, arr
+ 값이 변함
+ 모든 객체를 각각 생성해서 참조

<br>

# 2. 예시로 이해하기!

## Example 1) Immutable

```python
a = 100
b = 100
c = 100

print('id(a): \t\t', id(a))
print('id(b): \t\t', id(b))
print('id(c): \t\t', id(c))
```

```bash
id(a): 		 4348906832
id(b): 		 4348906832
id(c): 		 4348906832
```
{: file="Output"}

+ Immutable한 객체는 값이 같은 경우, 변수에 상관없이 동일한 주소를 참조한다.

![fig1](/statics/posts/python/py-13-fig1.jpg){: width="500"}

<br>

```python
c += 1

print('new c: \t\t', c)
print('id(c): \t\t', id(c))
```

```bash
new c: 		 101
id(c): 		 4348906864
```
{: file="Output"}

+ Immutable한 객체는 값이 다른 경우, 새로운 주소에 객체를 생성하여 참조하게 된다.

![fig2](/statics/posts/python/py-13-fig2.jpg){: width="500"}

<br>

## Example 2) Mutable

```python
d = [1, 2, 3]
e = [1, 2, 3]
f = [1, 2, 3]

print('id(d): \t\t', id(d))
print('id(e): \t\t', id(e))
print('id(f): \t\t', id(f))
```

```bash
id(d): 		 4669765312
id(e): 		 4669522240
id(f): 		 4425056128
```
{: file="Output"}

+ Mutable한 객체는 값이 같은 경우에도 모든 객체를 각각 생성해서 참조한다.

![fig3](/statics/posts/python/py-13-fig3.jpg){: width="500"}

<br>

```python
f.append(4)

print('new f: \t\t', f)
print('id(f): \t\t', id(f))
```

```bash
new f: 		 [1, 2, 3, 4]
id(f): 		 4425056128
```
{: file="Output"}

+ Mutable한 객체는 같은 변수의 값이 변경되더라도 객체의 주소는 변하지 않는다.

![fig4](/statics/posts/python/py-13-fig4.jpg){: width="500"}

<br>

## Example 3) Mutable & Immutable

```python
g = 'hello'
h = ['hello', 'python']

print('id(g): \t\t', id(g))
print('id(h): \t\t', id(h))
print('id(h[0]): \t', id(h[0]))
print('id(h[1]): \t', id(h[1]))
```

```bash
id(g): 		 4426431088
id(h): 		 4669503552
id(h[0]):        4426431088
id(h[1]):        4369162480
```
{: file="Output"}

+ `g` 변수가 바인딩하는 문자열 객체 `hello`와 h 리스트의 `[0]`번째 원소가 바인딩하는 문자열 `hello`는 동일한 객체! 주소 값을 확인해보면 두 같은 객체임을 알 수 있다.

![fig5](/statics/posts/python/py-13-fig5.jpg){: width="500"}

<br>

## Example 4) Immutable 및 mutable 객체 변경

Immutable 객체를 변경한다면?

```python
i = 'apple'
print('id(i): \t\t', id(i))
```

```bash
id(i): 		 4574351536
```
{: file="Output"}

```python
i = 'pineapple'
print('id(i): \t\t', id(i))
```

```bash
id(i): 		 4578009072
```
{: file="Output"}

+ `i` 변수에 대한 코드는 아래 스텝에 의해 수행된다.
  + step 1) `apple`이라는 문자열 객체가 메모리 `4574351536` 번지에 할당되고 해당 객체를 `i`라는 변수가 바인딩한다.
  + step 2) `pineapple`이라는 문자열 객체가 메모리 `4578009072` 번지에 할당되고 해당 객체를 `i`라는 변수가 바인딩한다.
  + step 3) `apple`이라는 문자열 객체는 아무도 자신을 참조하지 않기 때문에 garbage collector에 의해 자동으로 메모리에서 소멸된다.

+ 즉, 문자열 객체는 변경 불가능하기 때문에 기존 객체는 그대로 있고 새로운 문자열 객체가 생성되게 된다. 변수가 새로 생성된 문자열 객체를 바인딩하게 되면 기존 문자열 객체는 garbage collector에 의해 자동으로 소멸되게 되는 것이다.

<br>

Mutable 객체를 변경한다면?

```python
j = ['apple', 'pineapple']
print('id(j): \t\t', id(j))

j.append('orange')
print('new j: \t\t', j)
print('id(j): \t\t', id(j))
print('id(j[0]): \t', id(j[0]))
print('id(j[1]): \t', id(j[1]))
print('id(j[2]): \t', id(j[2]))
```

```bash
id(j): 		 4425040256
new j: 		 ['apple', 'pineapple', 'orange']
id(j): 		 4425040256
id(j[0]):        4574351536
id(j[1]):        4578009072
id(j[2]):        4573311920
```
{: file="Output"}

+ `j`라는 변수는 리스트 객체를 바인딩한다. 리스트는 mutable 객체로 리스트에 값을 추가해도 리스트 객체의 주소 값은 변하지 않고 동일하다.
+ 리스트 객체는 `[0]`번째, `[1]`번째, `[2]`번째 리스트 원소인 문자열 객체를 다시 바인딩하는 구조를 이룬다. 따라서 리스트에 새로운 원소를 추가하거나 삭제해도 리스트 객체의 주소 값이 변하지 않고 동일한 것이다.

![fig6](/statics/posts/python/py-13-fig6.jpg){: width="500"}

<br>

# Reference

+ [레벨업 파이썬 - Immutable과 Mutable](https://wikidocs.net/91520)
+ [개발자 지망생 - 파이썬 mutable, immutable 객체에 관해서](https://blockdmask.tistory.com/570)
