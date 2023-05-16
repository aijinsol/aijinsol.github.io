---
title: "[Python] Class - Practice"
excerpt: ""
categories:
  - Python
tags:
  - class
  - namespace
  - self
  - class variable
  - instance variable
  - class method
  - instance method
  - inflearn
last_modified_at: 2023-02-22
---

> [인프런 - 프로그래밍 시작하기: 파이썬 입문(Inflearn Original)](https://www.inflearn.com/course/%ED%94%84%EB%A1%9C%EA%B7%B8%EB%9E%98%EB%B0%8D-%ED%8C%8C%EC%9D%B4%EC%8D%AC-%EC%9E%85%EB%AC%B8-%EC%9D%B8%ED%94%84%EB%9F%B0-%EC%98%A4%EB%A6%AC%EC%A7%80%EB%84%90) 강의를 수강하고 기억할 내용을 제 언어로 요약정리한 포스팅이라 표현 및 설명이 매끄럽지 않을 수 있습니다!

# 1. 강의정보
 + Chapter06-1 <br>
 + Python Class <br>
 + `OOP`(객체 지향 프로그래밍), `Self`, `Instance Method`, `Instance Variable`

<br>

# 2. keywords
+ `class`(붕어빵기계) vs. `instance`(붕어빵) 차이 이해
+ `namespace`: 객체를 인스턴스화 할 때 저장된 공간 (각각 갖고 있는 자신만의 방)
+ `클래스 변수`: 직접 접근 가능, 공유
+ `인스턴스 변수`: 객체마다 별도 존재
+ `Self`: 인스턴스화 된 대상. 파이썬 코드에서 메모리에 올라가는 것
+ `클래스 메소드`: 클래스로 직접 호출 (매개변수가 없기 때문)
+ `인스턴스 메소드`: 인스턴스를 넘겨주거나 인스턴스로 호출 (`self`가 있기 때문)
+ `__init__`: 생성자
+ `__del__`: 소멸자

<br>

# 3. Examples

## Example 1) 클래스 변수 vs. 인스턴스 변수 이해

```python
class Dog:                  # object 상속
    # class 속성
    species = 'maltese'    # "클래스 변수"

    # 초기화/instance 속성
    def __init__(self, name, age):
        self.name = name    # self가 붙은 변수는 "인스턴스 변수"!
        self.age = age
```
+ 모든 class는 object를 상속받는다. 따라서 아래 세개의 표현 모두 가능. 어느 것을 써도 상관없다.
    + `class Dog(object)`: object 상속받는다고 명시적으로 써준 것
    + `class Dog()`
    + `class Dog`
+ `__init__`
    + 모든 class가 초기화될 때 반드시 호출되는 함수
    + 즉, 모든 class는 초기화 메소드를 갖는다

<br>

### 클래스 정보 출력
```python 
print(Dog)
```
```
--------------------------------------------------
<class '__main__.Dog'>
```

<br>

### 인스턴스화
```python
a = Dog('Mickey', 2)       # __init__ 메소드에서 넣어줬던 변수를 넣어준다.
b = Dog('Minnie', 3)
c = Dog('Mickey', 2)

# 비교
print(a == b, id(a), id(b), id(c))     
```
```
--------------------------------------------------
false 4409673088 4409672992 4409672848
```
+ 인스턴스 `a`와 인스턴스 `c`의 경우, 내용이 같아도 인스턴스끼리는 서로 다른 인스턴스!
    + 즉, 인스턴스화시킨 것은 서로 모두 다른 인스턴스!

<br>

### `namespace` 확인

```python
print('dog1', a.__dict__)
print('dog2', b.__dict__)
```
```
--------------------------------------------------
dog1 {'name': 'Mickey', 'age': 2}
dog2 {'name': 'Minnie', 'age': 3}
```
+ `namespace`: 객체를 인스턴스화할 때 저장되는 자신만의 공간
    + `{인스턴스변수}.__dict__` 이용하여 `namespace`에 접근 가능
    + class가 갖고 있는 attribute 확인 가능
    + dictionary 형태로 가져다 쓸 수 있게끔 출력된다.

<br>

### 인스턴스 속성 확인
```python
print('{} is {} and {} is {}'.format(a.name, a.age, b.name, b.age))
```
```
--------------------------------------------------
Mickey is 2 and Minnie is 3
```

<br>

```python
if a.species == 'maltese':
    print('{0} is a {1}'.format(a.name, a.species))

print(Dog.species)  # 클래스로 직접 접근 가능
print(a.species)    # 인스턴스화된 변수로도 접근 가능
print(b.species)
```
```
--------------------------------------------------
Mickey is a maltese
maltese
maltese
maltese
```

<br>

## Example 2) Self 이해

```python
class  SelfTest:
    # 클래스 메소드
    def func1():
        print('Func1 called')

    # 인스턴스 메소드
    def func2(self):
        print('Func2 called')
```
+ `Self`: 인스턴스화 된 대상. 파이썬 코드에서 메모리에 올라가는 것
+ 위 예시 코드에서 `__init__` 메소드가 없다!
    + 이렇게 `__init__` 메소드가 없는 경우, 파이썬이 내부적으로 알아서 실행해준다
    + 위 코드에서는 전 예시의 `name`이나 `age`같은 속성이 필요없고 기본 클래스로 사용하기 위해 만들어주지 않은 것
+ `클래스 메소드`
    + `self`가 없는 것은 클래스 메소드! 매개변수가 없기 때문에 클래스로 직접 호출한다
    + ex) `SelfTest.func1()`
+ `인스턴스 메소드`
    + `self`가 붙은 것은 인스턴스 메소드! 인스턴스를 넘겨주거나 인스턴스로 호출해야 한다
    + ex) `f.func2()` or `SelfTest.func2(f)`

<br>

### 인스턴스화
```python
f = SelfTest()

print(dir(f))
print(id(f))
```
```
--------------------------------------------------
['__class__', '__delattr__', '__dict__', '__dir__', '__doc__', '__eq__', '__format__', '__ge__', '__getattribute__', '__gt__', '__hash__', '__init__', '__init_subclass__', '__le__', '__lt__', '__module__', '__ne__', '__new__', '__reduce__', '__reduce_ex__', '__repr__', '__setattr__', '__sizeof__', '__str__', '__subclasshook__', '__weakref__', 'func1', 'func2']
4343498496
```
+ `dir(f)` 출력시 맨 끝에 내가 만들어준 메소드인 `func1()`, `func2()`가 잘 들어가 있다!

<br>

`func1()`, `func2()`를 호출해보자.

```python
f.func1()
```
```
--------------------------------------------------
TypeError: func1() takes 0 positional arguments but 1 was given
```
+ 에러 이유: `self`에 대한 인스턴스 값인 `f`을 넘겼는데 `self`가 없어서 `f`를 받을 수 없어서 예외 발생

<br>

```python
f.func2()
```
```
--------------------------------------------------
Func2 called
```
+ `self`는 인스턴스를 요구한다. `self`에 인스턴스 `f`가 넘어간 것

    ```python
    class  SelfTest:

    def func1():
        print('Func1 called')

    def func2(self):
        print(id(self))
        print('Func2 called')
    
    f = SelfTest()

    print(id(f))
    f.func2()
    ```
    ```
    --------------------------------------------------
    4343498496
    4343498496
    Func2 called
    ```
    + 클래스 내부 메소드 `func2()` 안에 넘어온 `self`의 id값과 클래스를 인스턴스화시킨 `f`의 id값이 같은 것을 볼 수 있다.

<br>

그럼 `func1()`은 어떻게 호출해야 할까? → 클래스로 바로 접근!

```python
SelfTest.func1()
```
```
--------------------------------------------------
Func1 called
```
+ `클래스 메소드`는 클래스로 바로 접근해서 호출할 수 있는 메소드
+ `인스턴스 메소드`는 클래스로 인스턴스화 시킨 변수로 접근해서 호출할 수 있는 메소드

<br>

```python
SelfTest.func2()
```
```
--------------------------------------------------
TypeError: func2() missing 1 required positional argument: 'self'
```
+ 에러 이유: 인스턴스화 시킨 변수를 넣어줘야 에러가 안 날 것!

<br>

```python
SelfTest.func2(f)
```
```
--------------------------------------------------
Func2 called
```
+ 인스턴스화 시킨 변수를 넣어주니 에러가 나지 않았다.

<br>

## Example 3) 생성자, 소멸자

```python
class Warehouse:
    # 클래스 변수: 모두와 공유
    stock_num = 0   # 재고

    # 생성자
    def __init__(self, name):
        # 인스턴스 변수: 나만의 속성
        self.name = name
        Warehouse.stock_num += 1

    # 소멸자
    def __del__(self): 
        Warehouse.stock_num -= 1
```
+ `__del__` 메소드: 객체가 소멸할 때 자동으로 호출되는 함수
    + `Warehouse.stock_num += 1`: 객체가 하나 만들어질 때 `stock_num`이 1 증가
    + `Warehouse.stock_num -= 1`: 객체가 하나 소멸할 때 `stock_num`이 1 감소

<br>

### 인스턴스화
```python
user1 = Warehouse('Kim')
user2 = Warehouse('Lee')
```

<br>

이 시점에 어떠한 일이 일어났을까?
```python
print(Warehouse.stock_num)
```
```
--------------------------------------------------
2
```
+ 두 번 인스턴스화 시켰으니까 2가 출력된다.

<br>

증명해보자
```python
print(user1.name)
print(user2.name)

print(user1.__dict__)
print(user2.__dict__)

print(Warehouse.__dict__)
```
```
--------------------------------------------------
Kim
Lee
{'name': 'Kim'}
{'name': 'Lee'}
{'__module__': '__main__', 'stock_num': 2, '__init__': <function Warehouse.__init__ at 0x1069ad0d0>, '__del__': <function Warehouse.__del__ at 0x1069ad160>, '__dict__': <attribute '__dict__' of 'Warehouse' objects>, '__weakref__': <attribute '__weakref__' of 'Warehouse' objects>, '__doc__': None}
```
+ `'stock_num': 2`로 바뀌어있는 것을 확인할 수 있다
+ `stock_num`은 모두와 공유한다는데 왜 `print(user1.__dict__)` 또는 `print(user2.__dict__)` 할 때는 `'stock_num': 2`가 나오지 않을까?
+ 그런데 아래와 같이 `user1.stock_num`와 `user2.stock_num`을 출력해보면 2라고 답이 잘 나온다. `stock_num` 정보가 잘 저장되어 있는 것

<br>

```python
print(user1.stock_num)
print(user2.stock_num)
```
```
--------------------------------------------------
2
2
```
+ 모두와 공유하는 정보이기 때문에 굳이 정보를 표시해주지 않은 것!
+ 인스턴스 `namespace`에 없는 정보라면, 공유하는 정보는 클래스의 `namespace`인 `(Warehouse.__dict__)`에서 정보를 가져온다.

<br>

```python
print('before:', Warehouse.stock_num) 

Warehouse.stock_num = 50
print('after:', Warehouse.stock_num)
```
```
--------------------------------------------------
before: 2
after: 50
```

<br>

클래스 변수에 직접 접근해서 수정도 가능. But, 좋은 방법은 아니다! 중요한 코드라면 더더욱 조심

```python
print(Warehouse.__dict__)
```
```
--------------------------------------------------
{'__module__': '__main__', 'stock_num': 50, '__init__': <function Warehouse.__init__ at 0x1061314c0>, '__del__': <function Warehouse.__del__ at 0x106131550>, '__dict__': <attribute '__dict__' of 'Warehouse' objects>, '__weakref__': <attribute '__weakref__' of 'Warehouse' objects>, '__doc__': None}
```
+ `'stock_num': 50`으로 바뀌어있는 것을 확인할 수 있다

<br>

### `__del__` 메소드 사용

```python
del user1
print('after delete user1:', Warehouse.__dict__)
```
```
--------------------------------------------------
after delete user1: {'__module__': '__main__', 'stock_num': 49, '__init__': <function Warehouse.__init__ at 0x104c0e430>, '__del__': <function Warehouse.__del__ at 0x104c0e4c0>, '__dict__': <attribute '__dict__' of 'Warehouse' objects>, '__weakref__': <attribute '__weakref__' of 'Warehouse' objects>, '__doc__': None}
```
+ `'stock_num': 49`로 바뀌어있는 것을 확인할 수 있다

<br>

## Example 4) 연습

```python
class Dog:
    species = 'maltese'

    def __init__(self, name, age):
        self.name = name
        self.age = age
    
    def info(self):
        return '{} is {} years old'.format(self.name, self.age)

    def bark(self, sound):
        return "{} barks {}!".format(self.name, sound)

# 인스턴스 생성
c = Dog('July', 4)
d = Dog('May', 10)

# 메소드 호출
print(c.info())
print(d.info())
print(c.bark('Woof Woof'))
print(d.bark('Woof!'))
```
```
--------------------------------------------------
July is 4 years old
May is 10 years old
July barks Woof Woof
May barks Woof!
```

<br>

# Reference
+ [인프런 - 프로그래밍 시작하기: 파이썬 입문(Inflearn Original)](https://www.inflearn.com/course/%ED%94%84%EB%A1%9C%EA%B7%B8%EB%9E%98%EB%B0%8D-%ED%8C%8C%EC%9D%B4%EC%8D%AC-%EC%9E%85%EB%AC%B8-%EC%9D%B8%ED%94%84%EB%9F%B0-%EC%98%A4%EB%A6%AC%EC%A7%80%EB%84%90)

<br>
