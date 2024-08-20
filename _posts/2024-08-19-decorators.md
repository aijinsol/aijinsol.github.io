---
title: "[Python] `dataclass`, `staticmethod`, `classmethod` 이해하기"
excerpt: ""
categories:
  - Python
tags:
  - decorators
  - dataclass
  - staticmethod
  - classmethod
last_modified_at: 2024-08-19
---

# 1. `dataclass`

## 1) 정의 및 기능
- `dataclass`는 데이터를 저장하는 데 사용하는 클래스를 쉽게 만들 수 있게 도와주는 Python 데코레이터다.
- 데이터를 저장하기 위한 클래스를 만들 때는 보통 `__init__`, `__repr__`, `__eq__` 등의 메서드를 정의해야 하는데, `dataclass`는 클래스를 정의할 때 이러한 메서드를 자동으로 생성해준다.

- 주요 기능
    - `__init__`: 클래스 생성자를 자동으로 생성
    - `__repr__`: 클래스의 객체를 출력할 때 사용하는 문자열을 자동으로 생성
    - `__eq__`: 클래스의 객체를 비교할 때 사용하는 메서드를 자동으로 생성

<br>

## 2) `dataclass` 사용 예시

- `dataclass`를 사용하지 않았을 때:
    ```python
    class Book:
        def __init__(self, title, author, pages):
            self.title = title
            self.author = author
            self.pages = pages

        def __repr__(self):
            return f"Book(title='{self.title}', author='{self.author}', pages={self.pages})"

        def __eq__(self, other):
            if not isinstance(other, Book):
                return False
            return self.title == other.title and self.author == other.author and self.pages == other.pages
    ```
    ```python
    book1 = Book("Python", "aijinsol", 300)
    book2 = Book("Python", "aijinsol", 300)

    print(book1)  # Output: Book(title='Python', author='aijinsol', pages=300)
    print(book1 == book2)  # Output: True
    ```

- `dataclass`를 사용했을 때:
    ```python
    from dataclasses import dataclass


    @dataclass
    class Book:
        title: str
        author: str
        pages: int
    ```
    ```python
    book1 = Book("Python", "aijinsol", 300)
    book2 = Book("Python", "aijinsol", 300)

    print(book1)  # Output: Book(title='Python', author='aijinsol', pages=300)
    print(book1 == book2)  # Output: True
    ```

❗ <b>결론</b>: `dataclass`를 사용하면 불필요한 코드를 줄이고, 클래스를 더욱 간결하게 정의할 수 있다.

<br>

# 2. `staticmethod`

## 1) 정의 및 기능
- `staticmethod`는 클래스 내부에서 정의되는 메서드이지만, 클래스나 인스턴스에 접근할 필요가 없는 경우에 사용한다. 즉, 함수처럼 동작하지만, 클래스 안에 정의되어 있다는 점이 다르다.
- `self`나 `cls`와 같은 매개변수가 필요없는 메서드를 정의할 때 사용한다.
- 주요 기능
    - 클래스의 인스턴스 없이 호출 가능
    - `self`나 `cls`같은 매개변수를 사용하지 않아도 됨
    - 클래스나 인스턴스의 상태를 변경하지 않음

<br>

## 2) `staticmethod` 사용 예시

- `staticmethod`를 사용하지 않았을 때:

    ```python
    class MathOperations:
        def multiply(self, a, b):
            return a * b
    ```
    ```python
    math_op = MathOperations()
    result = math_op.multiply(5, 3)

    print(result)  # Output: 15
    ```

- `staticmethod`를 사용했을 때:

    ```python
    class MathOperations:
        
        @staticmethod
        def multiply(a, b):
            return a * b
    ```
    ```python
    result = MathOperations.multiply(5, 3)

    print(result)  # Output: 15
    ```

❗ <b>결론</b>: `staticmethod`를 사용하면 클래스의 인스턴스를 생성할 필요 없이 메서드를 직접 호출할 수 있어 코드를 더 깔끔하게 작성할 수 있다.

<br>

# 3. `classmethod`

## 1) 정의 및 기능
- `classmethod`는 클래스 자체를 첫 번째 인자로 받는 메서드이다. 즉, 클래스의 인스턴스가 아닌 클래스 자체에 대한 작업을 수행할 때 유용하다.
- 주요 기능
    - 클래스 변수를 조작할 때 사용
    - 클래스를 통해 인스턴스를 생성할 때 사용

<br>

## 2) `classmethod` 사용 예시

- `classmethod`를 사용하지 않았을 때:

    ```python
    class Employee:
        raise_amount = 1.05  # Class variable

        def __init__(self, name, salary):
            self.name = name
            self.salary = salary
    ```
    클래스 변수 직접 수정
    ```python
    Employee.raise_amount = 1.2

    print(Employee.raise_amount)  # Output: 1.2
    ```
    문자열을 분리해 수동으로 객체 생성
    ```python
    employee_str = "aijinsol-100"
    name, salary = employee_str.split('-')
    employee = Employee(name, int(salary))

    print(employee.name)  # Output: aijinsol
    print(employee.salary)  # Output: 100
    ```

- `classmethod`를 사용했을 때:

    ```python
    class Employee:
        raise_amount = 1.05  # Class variable

        def __init__(self, name, salary):
            self.name = name
            self.salary = salary

        @classmethod
        def set_raise_amount(cls, amount):
            cls.raise_amount = amount

        @classmethod
        def from_string(cls, employee_str):
            name, salary = employee_str.split('-')
            return cls(name, int(salary))
    ```
    클래스 메서드 사용
    ```python
    Employee.set_raise_amount(1.2)

    print(Employee.raise_amount)  # Output: 1.2
    ```
    클래스 메서드로 객체 생성
    ```python
    employee = Employee.from_string("aijinsol-100")

    print(employee.name)  # Output: aijinsol
    print(employee.salary)  # Output: 100
    ```

<br>

💡 위 코드를 dataclass를 사용하는 코드로 바꿔보기!

```python
from dataclasses import dataclass
from typing import ClassVar


@dataclass
class Employee:
    raise_amount: ClassVar[int] = 1.05  # Class Variable with type hint
    name: str  # Instance Variable
    salary: int = 100 # Instance Variable with default value

    @classmethod
    def set_raise_amount(cls, amount):
        cls.raise_amount = amount

    @classmethod
    def from_string(cls, employee_str):
        name, salary = employee_str.split('-')
        return cls(name, int(salary))
```
```python
Employee.set_raise_amount(1.2)

print(Employee.raise_amount)  # Output: 1.2
```
```python
employee = Employee.from_string('aijinsol-200')

print(employee)  # Output: Employee(name='aijinsol', salary=200)
```
- `dataclass`를 사용해서 클래스를 정의할 때, class variable은 일반 클래스와 마찬가지로 정의하고, instance variable은 type hint를 사용해서 정의한다.
- class variable의 type hint는 `ClassVar`를 사용한다.
- instance variable의 default value가 있는 경우 `=`를 사용해서 지정한다.

❗ <b>결론</b>: `classmethod`를 사용하면 클래스메서드를 통해 더 직관적이고 간편하게 객체를 생성할 수 있다.
