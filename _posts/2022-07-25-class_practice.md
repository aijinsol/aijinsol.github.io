---
title: "[Python] Class - Practice"
excerpt: ""
categories:
  - python
tags:
  - class
  - inflearn
last_modified_at: 2022-07-25
---

> [인프런 - 프로그래밍 시작하기: 파이썬 입문(Inflearn Original)](https://www.inflearn.com/course/%ED%94%84%EB%A1%9C%EA%B7%B8%EB%9E%98%EB%B0%8D-%ED%8C%8C%EC%9D%B4%EC%8D%AC-%EC%9E%85%EB%AC%B8-%EC%9D%B8%ED%94%84%EB%9F%B0-%EC%98%A4%EB%A6%AC%EC%A7%80%EB%84%90) 강의를 수강하고 기억할 내용을 제 언어로 요약정리한 포스팅이라 표현 및 설명이 매끄럽지 않을 수 있습니다!

# 1. 강의정보
 + Chapter06-1 <br>
 + Python Class <br>
 + OOP(객체 지향 프로그래밍), Self, Instance Method, Instance Variable

<br>

# 2. keywords
+ `class`(붕어빵기계) & `instance`(붕어빵) 차이 이해
+ `namespace`: 객체를 인스턴스화 할 때 저장된 공간 (각각 갖고있는 자신만의 방)
+ `클래스 변수`: 직접 접근 가능, 공유
+ `인스턴스 변수`: 객체마다 별도 존재

<br>

# 3. Examples

## Example 1: 클래스 변수 & 인스턴스 변수 이해

```python
class Dog: # 모든 class는 object를 상속받는다. class Dog(object)라고 써도 상관없다.
    # class 속성
    species = 'firstdog'    # "클래스 변수"

    # 초기화/인스턴스 속성 (모든 class는 초기화 메소드를 갖는다.)
    def __init__(self, name, age):
        self.name = name    # self가 붙은 변수는 "인스턴스 변수"!
        self.age = age
```

```python
# 클래스 정보 출력
print(Dog)      # <class '__main__.Dog'>


# 인스턴스화
a = Dog('mickey', 2)       # __init__ 메소드에서 넣어줬던 변수를 넣어준다.
b = Dog('minnie', 3)
c = Dog('mickey', 2)


# 비교
print(a == b, id(a), id(b), id(c))     # false 4409673088 4409672992 4409672848
# 내용이 같아도 인스턴스끼리는 서로 다른 인스턴스!


# namespace: 객체를 인스턴스화할 때 자신만의 저장된 공간
# dictionary 형태로 가져다 쓸 수 있게끔 출력된다.
print('dog1', a.__dict__)   # dog1 {'name': 'mickey', 'age': 2}
print('dog2', b.__dict__)   # dog2 {'name': 'minnie', 'age': 3}


# 인스턴스 속성 확인
print('{} is {} and {} is {}'.format(a.name, a.age, b.name, b.age))     # mickey is 2 and minnie is 3

if a.species == 'firstdog':
    print('{0} is a {1}'.format(a.name, a.species))     # mickey is a firstdog

print(Dog.species)      # firstdog 클래스로 접근 가능
print(a.species)        # firstdog 인스턴스로도 접근 가능
print(b.species)        # firstdog
```

<br>

## Example 2: Self 이해

```python
# Self: 인스턴스화 된 대상. 파이썬 코드에서 메모리에 올라가는 것

class  SelfTest:

    # 클래스 메소드
    ## 매개변수가 없기 때문에 클래스로 직접 호출한다
    ## ex) SelfTest.func1()
    def func1():
        print('Func1 called')

    # 인스턴스 메소드
    ## self가 붙은 것은 인스턴스 메소드! 인스턴스를 넘겨주거나 인스턴스로 호출해야 한다
    ## ex) f.func2() or SelfTest.func2(f)
    def func2(self):
        print('Func2 called')
        print(id(self))
```

```python
# 인스턴스화
f = SelfTest()

print(dir(f))
# ['__class__', '__delattr__', '__dict__', '__dir__', '__doc__', '__eq__', 
# '__format__', '__ge__', '__getattribute__', '__gt__', '__hash__', '__init__', '__init_subclass__', 
# '__le__', '__lt__', '__module__', '__ne__', '__new__', '__reduce__', '__reduce_ex__', '__repr__', 
# '__setattr__', '__sizeof__', '__str__', '__subclasshook__', '__weakref__', 'func1', 'func2']

print(id(f))    # 4343498496


f.func1()   # TypeError: func1() takes 0 positional arguments but 1 was given
# 에러 이유: self에 대한 인스턴스 값인 f을 넘겼는데 self가 없어서 f를 받을 수 없어서 예외 발생

f.func2()   # Func2 called  4343498496
# self는 인스턴스를 요구한다. self에 인스턴스 f가 넘어간 것


SelfTest.func1()    # Func1 called
# 클래스로 바로 접근해서 호출할 수 있는 메소드

SelfTest.func2()    # TypeError: func2() missing 1 required positional argument: 'self'
# 에러 이유: 인스턴스화 시킨 변수를 넣어줘야 에러가 안 날 것!

SelfTest.func2(f)   # Func2 called  4343498496
```

<br>

## Example 3: 클래스 변수, 인스턴스 변수 실습

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
    def __del__(self):   # __del__ 메소드: 객체가 소멸할 때 자동으로 호출되는 함수
        Warehouse.stock_num -= 1
```

```python
# 인스턴스화
user1 = Warehouse('Lee')
user2 = Warehouse('Cho')


# 이 시점에 어떠한 일이 일어났을까?
print(Warehouse.stock_num)    # 2
# 2번 인스턴스화 시켰으니까 2가 출력된다.


##############################
# 증명해보자
##############################

print(user1.name)   # Lee
print(user2.name)   # Cho

print(user1.__dict__)   # user1의 namespace에 접근  # {'name': 'Lee'}
print(user2.__dict__)   # user2의 namespace에 접근  # {'name': 'Cho'}

print(Warehouse.__dict__)
# {'__module__': '__main__', 'stock_num': 2, '__init__': <function Warehouse.__init__ at 0x1069ad0d0>, 
# '__del__': <function Warehouse.__del__ at 0x1069ad160>, '__dict__': <attribute '__dict__' of 'Warehouse' objects>, 
# '__weakref__': <attribute '__weakref__' of 'Warehouse' objects>, '__doc__': None}
## 'stock_num: 2'로 바뀌어있는 것을 확인할 수 있다


# stock_num은 모두와 공유한다는데 왜 print(user1.name)할 때는 'stock_num': 2가 나오지 않을까?
# 그런데 아래와 같이 user1.stock_num을 출력해보면 2라고 답이 잘 나온다. stock_num 정보가 잘 저장되어 있는 것
print(user1.stock_num)  # 2
print(user2.stock_num)  # 2
# 모두와 공유하는 정보이기 때문에 굳이 정보를 표시해주지 않은 것!
# 인스턴스 namespace에 없는 정보라면, 공유하는 정보는 클래스의 namespace (Warehouse.__dict__)에서 정보를 가져온다


# 클래스 변수에 직접 접근해서 수정도 가능. But, 좋은 방법은 아니다! 중요한 코드라면 더더욱 조심
print('before:', Warehouse.stock_num)   # before: 2
Warehouse.stock_num = 50
print('after:', Warehouse.stock_num)    # after: 50

print(Warehouse.__dict__)
# {'__module__': '__main__', 'stock_num': 50, '__init__': <function Warehouse.__init__ at 0x1061314c0>, 
# '__del__': <function Warehouse.__del__ at 0x106131550>, '__dict__': <attribute '__dict__' of 'Warehouse' objects>, 
# '__weakref__': <attribute '__weakref__' of 'Warehouse' objects>, '__doc__': None}
## 'stock_num: 50'으로 바뀌어있는 것을 확인할 수 있다


# __del__ 메소드 사용
del user1
print('after delete user1:', Warehouse.__dict__)
# after delete user1: {'__module__': '__main__', 'stock_num': 49, '__init__': <function Warehouse.__init__ at 0x104c0e430>, 
# '__del__': <function Warehouse.__del__ at 0x104c0e4c0>, '__dict__': <attribute '__dict__' of 'Warehouse' objects>, 
# '__weakref__': <attribute '__weakref__' of 'Warehouse' objects>, '__doc__': None}
## 'stock_num: 49'로 바뀌어있는 것을 확인할 수 있다
```

## Example 4: 연습

```python
class Dog:
    species = 'firstdog'

    def __init__(self, name, age):
        self.name = name
        self.age = age
    
    def info(self):
        return '{} is {} years old'.format(self.name, self.age)

    def speak(self, sound):
        return "{} says {}!".format(self.name, sound)


# 인스턴스 생성
c = Dog('July', 4)
d = Dog('May', 10)


# 메소드 호출
print(c.info())     # July is 4 years old
print(d.info())     # May is 10 years old
print(c.speak('Woof Woof'))       # July says Woof Woof!
print(d.speak('Ouch!'))     # May says Ouch!
```


<br>

# Reference
+ [인프런 - 프로그래밍 시작하기: 파이썬 입문(Inflearn Original)](https://www.inflearn.com/course/%ED%94%84%EB%A1%9C%EA%B7%B8%EB%9E%98%EB%B0%8D-%ED%8C%8C%EC%9D%B4%EC%8D%AC-%EC%9E%85%EB%AC%B8-%EC%9D%B8%ED%94%84%EB%9F%B0-%EC%98%A4%EB%A6%AC%EC%A7%80%EB%84%90)

<br>
