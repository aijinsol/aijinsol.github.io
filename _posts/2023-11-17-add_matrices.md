---
title: "[Python] Matrix Operation Functions"
excerpt: "행렬의 덧셈 연산 함수 구현"
categories:
  - Python
tags:
  - numpy
  - list comprehension
  - zip
last_modified_at: 2023-11-17
---

> 행렬의 덧셈 연산을 수행하는 함수를 만들어보자!

# Solution 1) `numpy` 이용

- `numpy`의 `array`를 사용하여 행렬을 생성하고, 두 행렬의 크기를 비교한 후 덧셈 수행
- 마지막에 결과를 다시 Python의 list로 변환하여 반환

```python
import numpy as np

def add_matrices(matrix1, matrix2):
    np_matrix1 = np.array(matrix1)
    np_matrix2 = np.array(matrix2)

    if np_matrix1.shape != np_matrix2.shape:
        raise ValueError("Matrix dimensions do not match.")

    result = np_matrix1 + np_matrix2
    return result.tolist()  # Convert NumPy array back to a Python list
```

```python
# Example usage:
matrix1 = [[1, 2, 3], [4, 5, 6], [7, 8, 9]]
matrix2 = [[9, 8, 7], [6, 5, 4], [3, 2, 1]]

result_matrix = add_matrices(matrix1, matrix2)
print(result_matrix)
```

```
--------------------------------------------------
[[10, 10, 10], [10, 10, 10], [10, 10, 10]]
```

<br>

# Solution 2) 이중 for문

- Loop(이중 for문)을 이용하여 행렬 덧셈 수행
- 행렬의 크기 확인 후, 각 원소를 더한 후에 결과 list에 추가
- 장단점
  - 명시적인 이중 loop 구조로, 가독성이나 유지보수 측면에서 코드의 동작이 명확하게 드러난다
  - 큰 행렬에서 메모리 사용량이 작을 수 있다
  - Loop를 사용하므로 성능 면에서 list comprehension에 비해 느릴 수 있다

```python
def add_matrices(matrix1, matrix2):
    if len(matrix1) != len(matrix2) or len(matrix1[0]) != len(matrix2[0]):
        raise ValueError("Matrix dimensions do not match.")

    result = []
    for i in range(len(matrix1)):
        row = []
        for j in range(len(matrix1[0])):
            row.append(matrix1[i][j] + matrix2[i][j])
        result.append(row)
    return result
```

```python
# Example usage:
matrix3 = [[1, 2, 3], [4, 5, 6], [7, 8, 9]]
matrix4 = [[9, 8, 7], [6, 5, 4], [3, 2, 1]]

result_matrix = add_matrices(matrix3, matrix4)
print(result_matrix)
```

```
--------------------------------------------------
[[10, 10, 10], [10, 10, 10], [10, 10, 10]]
```

<br>

# Solution 3) List Comprehension + `zip` 함수 이용

- List comprehension을 이용하게 간단하게 작성
- `zip` 함수를 이용하여 두 행렬의 각 행의 원소를 묶어서 덧셈 수행하고, 그 결과를 새로운 list로 만든다
- 장단점
  - 간결하고 짧은 코드
  - 큰 행렬에서는 list comprehension을 사용할 경우 메모리 사용량이 증가할 수 있다
  - 오류 발생시 어디서 오류가 발생했는지 정보가 부족하다. 즉, 디버깅이 어려울 수 있다

```python
def add_matrices(matrix1, matrix2):
    if len(matrix1) != len(matrix2) or len(matrix1[0]) != len(matrix2[0]):
        raise ValueError("Matrix dimensions do not match.")

    result = [[c + d for c, d in zip(a, b)] for a, b in zip(matrix1, matrix2)]
    return result
```

```python
# Example usage:
matrix5 = [[1, 2, 3], [4, 5, 6], [7, 8, 9]]
matrix6 = [[9, 8, 7], [6, 5, 4], [3, 2, 1]]

result_matrix = add_matrices(matrix5, matrix6)
print(result_matrix)
```

```
--------------------------------------------------
[[10, 10, 10], [10, 10, 10], [10, 10, 10]]
```

<div class="notice--info" markdown="1">
💡 <b>"Loop를 사용하거나 list comprehension을 사용하는 경우에 따라 큰 행렬에서 메모리 사용량이 달라질 수 있다??"</b>

- `List comprehension`보다 `Loop`를 사용하여 각 원소를 하나씩 처리하는 방식이 메모리를 덜 사용할 수 있다는 것을 의미

- `Loop`를 사용하면 각 원소를 순회하면서 하나씩 연산하고 결과를 저장하기 때문에, 큰 행렬에서도 한 번에 많은 양의 메모리를 사용하지 않는다. 대신에 각 행의 결과를 하나씩 만들어가면서 최종 결과를 얻는다

- `List comprehension`을 사용하여 한 번에 모든 연산을 처리하면, 큰 행렬의 경우 그 크기에 비례하여 많은 메모리를 사용할 수 있다. 이는 모든 결과를 한 번에 생성하여 list에 저장하게 되기 때문

- 결론: 주로 Python의 list comprehension과 같이 한 번에 모든 원소를 처리하는 방식이 큰 데이터셋에서 메모리 사용량이 더 많아질 수 있다는 일반적인 원리! ⭐️
</div>
