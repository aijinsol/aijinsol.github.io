---
title: "[Python] List del, remove, pop, slice 성능 비교"
description: "List에서 일부 요소를 삭제하거나 추출하는 4가지 방법(del, remove, pop, slice)의 성능 비교"
author: aijinsol
date: 2023-02-19
categories:
  - Python
tags:
  - list
---

# 1. del

+ `del a[x]`: a 리스트에서 x번째 요소값 삭제 

    (example)
    ```python
    a = [1,2,3]
    del a[1]
    print(a)
    ```
    ```bash
    [1, 3]
    ```
    {: file="Output"}

<br>

# 2. remove(x)

+ `remove(x)`: 리스트에서 첫번째로 나오는 x 삭제

    (example)
    ```python
    a = [1,2,3,1,2,3]
    a.remove(3)
    print(a)
    ```
    ```bash
    [1, 2, 1, 2, 3]
    ```
    {: file="Output"}

<br>

# 3. pop(), pop(x)

+ `pop()`: 리스트의 맨 마지막 요소 리턴 및 그 요소 삭제
    
    (example)
    ```python
    a = [1,2,3]
    popped_item = a.pop()

    print(a)
    print(popped_item)
    ```

    ```bash
    [1, 2]
    3
    ```
    {: file="Output"}

+ `pop(x)`: 리스트의 x번째 요소 리턴 및 그 요소 삭제

    (example)
    ```python
    a = [1,2,3]
    popped_item = a.pop(1)

    print(a)
    print(popped_item)
    ```

    ```bash
    [1, 3]
    2
    ```
    {: file="Output"}

<br>

# 4. slice

+ `slicing`은 위와 같이 리스트의 값을 삭제하는 것이 아닌 원하는 범위를 출력하는 것. 즉, 원본 리스트는 그대로 보존하고 원하는 범위만큼 새로운 리스트를 생성하기 위해 사용.

    (example)
    ```python
    a = [1,2,3,4,5]
    b = a[:2]
    c = a[2:]

    print(b)
    print(c)
    ```

    ```bash
    [1, 2]
    [3, 4, 5]
    ```
    {: file="Output"}

<br>

# 4. del, remove, pop, slice 성능비교
    
```python
import time

# del
start1 = time.time()
del a[9]
end1 = time.time()

# remove
start2 = time.time()
b.remove(10)
end2 = time.time()

# pop
start3 = time.time()
a.pop(10)
end3 = time.time()

# slice
start4 = time.time()
a[10:]
end4 = time.time()

print(f'del time: {end1 - start1:.10f} sec')
print(f'rem time: {end2 - start2:.10f} sec')
print(f'pop time: {end3 - start3:.10f} sec')
print(f'sli time: {end4 - start4:.10f} sec')
```

```bash
del time: 0.0008070469 sec
rem time: 0.0002350807 sec
pop time: 0.0002071857 sec
sli time: 0.0022301674 sec
```
{: file="Output"}

+ 결론
    + 가장 오래 걸리는 시간 순으로 `slice > del > remove > pop`
    + 원본 리스트를 보존하지 않아도 된다면 `pop`을 쓰는 것이 속도 측면에서 가장 유리할 것으로 보인다.
    + 물론 원본 리스트를 보존해야한다면 속도 측면에서 느리더라도 `slice`를 써야한다.
