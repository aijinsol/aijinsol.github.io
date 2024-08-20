---
title: "[Python] Decorator Practice"
excerpt: "유형별 decorator 만들기 연습 (feat. # of params)"
categories:
  - Python
tags:
  - decorators
last_modified_at: 2023-10-17
---

> Decorator를 사용하면, type 체크 또는 유효성 체크할 때 편리하다.

# 1. Parameter 없는 함수에 decorator 적용

```python
import datetime


def outer_func(func):
    def inner_func():
        print('>>> Time: ' + str(datetime.datetime.now()))
        func()
        print('>>> Decorated!!')
    return inner_func
```

<br>
► Decrator를 사용하지 않고 함수의 인자에 함수를 넣어 구현하면,

```python
def my_func():
    print('Main function called')


decorated_func = outer_func(my_func)
decorated_func()
```

```
--------------------------------------------------
Time: 2023-10-16 19:52:25.771635
>>> Main function called
Decorated!!
```

- 위 코드처럼 복잡하게 함수 인자로 함수를 넣지 않고, `decorator`을 써서 간단하게 구현하면 아래처럼 구현할 수 있다.
- 이렇게 구현하면 decoration하고 싶은 함수를 필요할 때마다 언제든 불러와 사용할 수 있다는 장점이 있다.

<br>

► Decorator를 사용하면,

```python
@outer_func
def my_func():
    print('Main function called')


my_func()
```

```
--------------------------------------------------
Time: 2023-10-16 19:52:25.771635
>>> Main function called
Decorated!!
```

- `log_func` 함수가 `outer_func` 함수의 인자로 들어가서 `outer_func(log_func)` 함수가 호출되는 것과 마찬가지!

<br>

# 2. Parameter 있는 함수에 decorator 적용

<div class="notice--info" markdown="1">
💡 <b>parameter 있는 함수에 decorator 적용 방법</b>

- inner function의 parameter와 outer function이 받는 function의 parameter를 동일하게 설정!
</div>

```python
def outer_func(func):
    def inner_func(num1, num2):
        if num2 == 0:
            print('Division by zero is not possible.')
            return
        func(num1, num2)
    return inner_func
```

<br>

► Decorator를 사용하지 않았을 때,

```python
def my_func(num1, num2):
    print(num1 / num2)


decorated_func = outer_func(my_func)
decorated_func(1, 0)
decorated_func(1, 2)
```

```
--------------------------------------------------
Division by zero is not possible.
0.5
```

<br>

► Decorator를 사용했을 때,

```python
@outer_func
def my_func(num1, num2):
    print(num1 / num2)


my_func(1, 0)
my_func(1, 2)
```

```
--------------------------------------------------
Division by zero is not possible.
0.5
```

<br>

# 3. Parameter 갯수와 상관없이 decorator 적용

<div class="notice--info" markdown="1">
💡 여러 개의 parameter를 넣고 싶을 때는 `*args`, `**kwargs` 사용!
</div>

```python
import datetime


def outer_func(func):
    def inner_func(*args, **kwargs):
        print('>>> Time: ' + str(datetime.datetime.now()))
        return func(*args, **kwargs)
    return inner_func
```

<br>

► Parameter 1개일 떄,

```python
@outer_func
def add_bar(word):
    print(word + '|' + word)


add_bar('hello')
```

```
--------------------------------------------------
>>> Time: 2023-10-17 09:13:57.860112
'hello|hello'
```

<br>

► Parameter 여러 개일 때,

```python
@outer_func
def add_bar(word1, word2, word3):
    print(word1 + '|' + word2 + '|' + word3)


add_bar('hello', 'thanks', 'bye')
```

```
--------------------------------------------------
>>> Time: 2023-10-17 09:17:01.529291
hello|thanks|bye
```

<br>

# 4. 한 함수에 여러 개의 decorator 지정

> 한 함수에 여러 개의 decorator를 지정할 수 있으며, decorator를 나열한 순서대로 실행된다.

## 1) parameter가 없는 경우

```python
def decorator1(func):
    def inner_func():
        print('decorator1')
        func()
    return inner_func

def decorator2(func):
    def inner_func():
        print('decorator2')
        func()
    return inner_func
```

<br>

```python
@decorator1
@decorator2
def say_hello():
    print('Hello')


say_hello()
```

```
--------------------------------------------------
decorator1
decorator2
Hello
```

<br>

## 2) parameter가 (여러 개) 있는 경우

```python
def tag_h1(func):
    def inner_func(*args, **kwargs):
        return '<h1>' + func(*args, **kwargs) + '</h1>'
    return inner_func


def tag_italic(func):
    def inner_func(*args, **kwargs):
        return '<i>' + func(*args, **kwargs) + '</i>'
    return inner_func
```

<br>

```python
@tag_h1
@tag_italic
def write_contents(contents):
    return contents

@tag_italic
@tag_h1
def write_words(word1, word2, word3):
    return word1 + '|' + word2 + '&' + word3


print(write_contents('Today is a great day!'))
print(write_words('hello', 'thanks', 'bye'))
```

```
--------------------------------------------------
<h1><i>Today is a great day!</i></h1>
<i><h1>hello|thanks&bye</h1></i>
```

<br>

# 5. Parameter가 있는 decorator

> 지금까지는 내가 실행할 함수에 parameter를 넣어줬었다. <br>
> 이번에는 decorator 자체에 parameter를 넣어보자.

<div class="notice--info" markdown="1">
💡 중첩함수를 총 3단계 깊이로 만들기!
</div>

```python
def tag_html(tag):
    def outer_func(func):
        def inner_func(*args, **kwargs):
            return '<' + tag + '>' + func(*args, **kwargs) + '</' + tag + '>'
        return inner_func
    return outer_func
```

<br>

► Decorator를 사용하지 않았을 때,

```python
def tag_h1(string):
    return string


decorated_func = tag_html('h1')(tag_h1)
print(decorated_func('hello'))
```

```
--------------------------------------------------
<h1>hello</h1>
```

<br>

► Decorator를 사용했을 때,

```python
@tag_html('h1')
def tag_h1(string):
    return string

@tag_html('i')
def tag_italic(string):
    return string

@tag_html('b')
def tag_bol(string):
    return string


print(tag_h1('Title'))
print(tag_italic('Writing in italic'))
print(tag_bold('Writing in bold'))
```

```
--------------------------------------------------
<h1>Title</h1>
<i>Writing in italic</i>
<b>Writing in bold</b>
```

<br>

# 6. Class의 method에 decorator 적용

- Class의 method에 decorator를 적용할 때 주의해야 할 사항!
  - Class의 method의 첫 parameter로 `self` 써 주는 것 잊지 않기!

```python
def tag_bold(func):
    def inner_func(self, *args, **kwargs):
        return f'<b>{func(self, *args, **kwargs)}</b>'
    return inner_func


class Student:
    def __init__(self, name, age, phone):
        self.name = name
        self.age = age
        self.phone = phone

    @tag_bold
    def greetings(self):
        return 'Good morning, ' + self.name + '!'
```

<br>

```python
jinsolkim = Student('Jinsol', 10, '010123')
print(jinsolkim.greetings())
```

```
--------------------------------------------------
<b>Good morning, Jinsol!</b>
```
