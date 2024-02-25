---
title: "[Python] 인스턴스 변수 선언 방식을 통한 파이썬에서의 데이터 캡슐화"
excerpt: "인스턴스 변수를 선언하기 위한 3가지 방식: 공개부터 비공개까지"
categories:
  - Python
tags:
  - class
  - instance variable
last_modified_at: 2024-02-25
---

# Intro

이번 포스팅에서는 파이썬에서 클래스 인스턴스 변수를 다루는 3가지 방법에 대해 정리해본다. 객체 지향 프로그래밍(OOP)의 핵심 특성 중 하나인 `캡슐화(Capsulation)`를 실제로 어떻게 구현할 수 있는지, 클래스 인스턴스 변수의 선언 방식을 통해 살펴본다.

본 포스팅을 통해, 공개(public) 인스턴스 변수에서부터 시작해, 내부 사용(protected) 인스턴스 변수를 거치고, 가장 민감한 정보를 다루는 비공개(private) 인스턴스 변수에 이르기까지 각각의 접근 수준이 어떻게 데이터 보호에 기여하는지를 알아볼 것이다. 이 과정을 통해, 사용자 데이터를 안전하게 관리하는 방법뿐만 아니라, 파이썬 프로그래밍 내에서 정보 캡슐화의 중요성에 대해서도 깊이 이해해보자!

<br>

# 1. 공개 인스턴스 변수

가장 기본적인 형태는 공개 인스턴스 변수이다. 이 변수들은 클래스 외부에서 자유롭게 접근하고 수정할 수 있다. 사용자 프로필 페이지와 같이 공개될 수 있는 정보에 적합하다.

```python
class UserProfile:
    def __init__(self, username: str, hobby: str):
        self.username = username
        self.hobby = hobby

user_profile = UserProfile(username='aijinsol', hobby='Running')

print(f'Username: {user_profile.username}, Hobby: {user_profile.hobby}')
```

```
Username: aijinsol, Hobby: Running
```

위 예시에서 `username`과 `hobby`는 공개적으로 접근 가능한 데이터를 나타낸다. 이 정보는 사용자가 공유하기로 선택한 내용이므로, 공개 인스턴스 변수로 설정하는 것이 적합하다.

<br>

# 2. 내부 사용 인스턴스 변수

이 변수들은 클래스 내부에서만 사용되도록 제한할 수 있다. 이 정보는 내부 프로세스나 특정 함수에서만 필요할 수 있으므로, 한 개의 언더스코어(`_`)를 사용하여 내부 사용 변수로 표시한다.

```python
class UserContactInfo:
    def __init__(self, email: str, phone_number: str):
        self._email = email
        self._phone_number = phone_number

user_contact = UserContactInfo(email='aijinsol@example.com', phone_number='123-4567')

print(f'Email: {user_contact._email}, Phone Number: {user_contact._phone_number}')
```

```
Email: aijinsol@example.com, Phone Number: 010-123-4567
```

위 예시에서 `_email`과 `_phone_number`는 내부 목적으로만 사용된다. 비록 파이썬이 접근을 엄격하게 제한하지 않지만, 이러한 정보는 일반적으로 사용자 인터페이스나 공개 API를 통해 노출되어서는 안 된다는 것을 의미한다.

<br>

# 3. 비공개 인스턴스 변수

이 변수들은 클래스 내부에서만 사용되어야 하며, 클래스 외부에서는 접근할 수 없도록 해야한다. 예를 들어, 사용자의 은행계좌나 신용카드번호와 가장 민감한 정보는 비공개로 유지되어야 한다. 이런 유형의 인스턴스는 두 개의 언더스코어(`__`)로 시작하는 비공개 인스턴스 변수로 정의된다.

이렇게 더블 언더스코어로 시작하는 것을 'Name Mangling' 기법이다. 이 매커니즘은 특히 클래스가 확장될 때, 부모 클래스와 자식 클래스 간에 변수 이름 충돌을 방지하는 데 유용하다. 맹글링을 통해, 클래스의 내부적인 사용을 위한 변수를 명확하게 정의할 수 있고, 클래스의 외부 인터페이스와는 분리하여 관리할 수 있다.

```python
class UserFinancialInfo:
    def __init__(self, bank_account: str, credit_card: str):
        self.__bank_account = bank_account
        self.__credit_card = credit_card

    def display_financial_info(self) -> str:
        return f'Bank Account: {self.__bank_account[-4:]}', f'Credit Card: {self.__credit_card[-4:]}'

user_financial = UserFinancialInfo(bank_account='123456789', credit_card='987654321')

print(user_financial.display_financial_info())
```

```
('Bank Account: 6789', 'Credit Card: 4321')
```

이 예시에서, `__bank_account`와 `__credit_card`는 비공개 변수이다. 이는 클래스 외부에서 직접 접근할 수 없으며, 정보를 보호하기 위해 정보의 일부만 노출하는 메서드를 통해 간접적으로 정보에 접근할 수 있다. 이런 방식으로 민감한 정보를 보호할 수 있다.

<br>

# Outro

파이썬에서 클래스 인스턴스 변수를 사용하는 방법 3가지를 알아보았다. 인스턴스 변수의 민감도에 따라 적절한 수준의 접근 제어를 적용하는 것이 매우 중요하다.

- **공개 인스턴스 변수**는 일반적인 정보 공유에 적합하다.
- **내부 사용 변수**는 민감한 정보를 다룰 때 추가적인 보호층을 제공한다.
- **비공개 변수**는 가장 예민한 정보를 안전하게 보호하는 데 필수적이다.

이러한 Python convention을 따르면서, 데이터 보호를 강화하고, 협업시에도 이해하기 쉽고 관리하기 편한 클린코드를 작성하도록 하자!
