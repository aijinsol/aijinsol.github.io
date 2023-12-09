---
title: "[CS] Linked List"
excerpt: "Python으로 Linked List 구현하기"
categories:
  - CS
tags:
  - Linked List
last_modified_at: 2023-12-09
---

<div class="notice--primary" markdown="1">
✔️ Python으로 LinkedList를 직접 구현해보자!
</div>

> ✏️ 함께 풀어보면 좋은 코딩테스트 문제: [LeetCode #1472. Design Browser History](https://leetcode.com/problems/design-browser-history/description/)

# 1. `Linked List` 간단히 알아보기

- `Linked List`는 List의 한 종류로, `List`는 `Array List`와 `Linked List`로 나뉜다.
- 참고로, Array List는 Array와 Dynamic Arrary로 구분되며, Python의 List는 Dynamic Array를 통해 구현되어 있다. 그런데 사실 Dynamic Array도 내부적으로 Array를 사용하여 구현되어 있다.

<br>

![image01](/assets/images/2023-12-09-linked_list-01.png){: .align-center}{: width="40%" height="40%"}

<br>

- `Linked List`는 데이터를 연속적인 메모리 공간에 저장하지 않고, 각 노드가 다음 노드를 가리키는 방식으로 데이터를 구조화하는 자료구조이다.
- Linked List는 데이터의 삽입과 삭제가 간편하며, 크기를 동적으로 조절할 수 있다는 장점을 가지고 있다.
- 또한, 메모리를 효율적으로 사용할 수 있는 특징이 있어, 특정 상황에서는 List보다 Linked List를 선택하는 것이 더 좋은 선택이 될 수 있다.
- 이러한 장점들로 인해 코딩테스트에서는 Linked List를 구현할 줄 아는 것이 중요하다.

<br>

이번 포스팅에서 Python을 이용하여 간단하게 `LinkedList`를 구현하는 방법을 알아보자!

<br>

# 2. `Linked List` 구현하기

## 1) `Node` 클래스 구현하기

- Linked List의 기본 단위은 `Node` 클래스를 먼저 정의한다.
- 각 노드는 값을 저장하는 `value`와 다음 노드를 가리키는 `next`를 가지고 있다.

```python
class Node:
    def __init__(self, value=0, next=None):
        self.value = value
        self.next = next
```

<br>

## 2) `LinkedList` 클래스 구현하기

- Linked List 자체를 나타내는 `LinkedList`를 구현한다.
- `LinkedList` 클래스는 노드들의 연결 정보를 가지고 있으며, 각 연산을 통해 리스트를 조작할 수 있다.

```python
class LinkedList(object):
    def __init__(self):
        self.head = None
        self.tail = None

    def append(self, value):  # Time Complexity: O(N)
        new_node = Node(value)
        if self.head is None:
            self.head = new_node
        else:
            current = self.head
            while current.next:
                current = current.next
            current.next = new_node

    def append_v2(self, value):  # Time Complexity: O(1)
        new_node = Node(value)
        if self.head is None:
            self.head = new_node
            self.tail = new_node
        else:
            self.tail.next = new_node
            self.tail = self.tail.next

    def get(self, idx):
        current = self.head
        for _ in range(idx):
            current = current.next
        return current.value

    def insert(self, idx, value):
        new_node = Node(value)
        current = self.head
        for _ in range(idx-1):
            current = current.next
        new_node.next = current.next
        current.next = new_node

    def remove(self, idx):
        # Garbage Collector removes the node
        if idx == 0:
            self.head = self.head.next
        else:
            current = self.head
            for _ in range(idx-1):
                current = current.next
            current.next = current.next.next
```

### 2-1) `append` 메소드

- 노드를 리스트의 끝에 추가하는 메소드
- 리스트의 모든 노드를 확인하며 끝을 찾기 때문에 시간복잡도는 `O(N)`

### 2-2) `append_v2` 메소드

- 최적화된 버전으로, 노드를 리스트의 끝에 추가하는 메소드
- 끝을 직접 가리키는 `tail` 포인터를 활용해 한 번의 연산으로 끝에 노드를 추가할 수 있어, 리스트의 길이에 상관없이 일정한 시간이 소요되므로, 시간복잡도는 `O(1)`

### 2-3) `get` 메소드

- 주어진 인덱스에 해당하는 노드의 값을 반환하는 메소드
- 이 연산은 인덱스에 비례하는 시간이 소요되므로 시간복작보는 `O(N)`

### 2-4) `insert` 메소드

- 주어진 인덱스에 새로운 값을 갖는 노드를 삽입하는 메소드
- 삽입 시에는 해당 인덱스를 찾아가는 과정이 필요하므로 시간복잡도는 `O(N)`

### 2-5) `remove` 메소드

- 주어진 인덱스에 해당하는 노드를 삭제하는 메소드
- 삭제 시에도 해당 인덱스를 찾아가는 작업이 필요하므로 시간복잡도는 `O(N)`

<br>

# 3. Examples

위에서 구현한 `Node`와 `LinkedList` 클래스를 이용해 Linked List 예시를 만들어보자.

```python
# Creating a linked list
my_linked_list = LinkedList()

# Adding values
my_linked_list.append(10)
my_linked_list.append(20)
my_linked_list.append(30)

# Displaying values
print("Values in the linked list:")
for i in range(3):
    print(f"Index {i}: {my_linked_list.get(i)}")

# Inserting a new value
my_linked_list.insert(1, 15)

# Displaying values after insertion
print("\nValues in the linked list after insertion:")
for i in range(4):
    print(f"Index {i}: {my_linked_list.get(i)}")

# Removing a value at a specific index
my_linked_list.remove(2)

# Displaying values after removal
print("\nValues in the linked list after removal:")
for i in range(3):
    print(f"Index {i}: {my_linked_list.get(i)}")
```

```
-------------------------------------------------
Values in the linked list:
Index 0: 10
Index 1: 20
Index 2: 30

Values in the linked list after insertion:
Index 0: 10
Index 1: 15
Index 2: 20
Index 3: 30

Values in the linked list after removal:
Index 0: 10
Index 1: 15
Index 2: 30
```
