---
title: "[NLP] Levenshtein Distance"
description: ""
author: aijinsol
date: 2022-07-21
categories:
  - NLP
tags:
---

# 1. Levenshtein Distance 이해하기

- `word1 → word2` 로 바꾸는 과정에서 수행되는 operation 갯수 (단어 간 distance 연산)
- `Edit Distance` 라고도 부른다.
- `OPERATIONS` 종류 <br>
  1. REPLACE <br>
  2. INSERT <br>
  3. DELETE <br>

<br>

# 2. Levenshtein Distance 연산하기

|         | word1 → | a   | b   | c   | d   | e   | f   |
| ------- | ------- | --- | --- | --- | --- | --- | --- |
| word2 ↓ | 0       | 1   | 2   | 3   | 4   | 5   | 6   |
| a       | 1       | `0` | 1   | 2   | 3   | 4   | 5   |
| z       | 2       | 1   | `1` | 2   | 3   | 4   | 5   |
| c       | 3       | 2   | 2   | `1` | `2` | 3   | 4   |
| e       | 4       | 3   | 3   | 2   | 2   | `2` | 3   |
| d       | 5       | 4   | 4   | 3   | 2   | 3   | `3` |

- 해당 값에 영향을 준 값들을 찾아 거슬러 올라가며 단어의 operation을 해석한다.
  - ↖︎ (다른숫자) : `word1` → `word2` change
  - ↖︎ (다른숫자) : no change
  - ← : `word1` delete

- 위의 예시 표 해석
  - `abcdef` → `azced`
  - 최종 levenshtein distance: `3` (표 가장 오른쪽 하단)
  - operations <br>
    1. `f` → `d` <br>
    2. `e` no change <br>
    3. `d` DELETE <br>
    4. `c` no change <br>
    5. `b` → `z` <br>
    6. `a` no change
       ⇒ `decza`, 순서 배열 뒤집으면 원하는 값인 `azced` 가 나온다!

<br>

# 3. Levenshtein Distance 코드

```python
if str1[i] == str2[j]:
	T[i][j] = T[i-1][j-1]
else:
	T[i][j] = min(T[i-1][j], T[i-1][j-1], T[i][j-1]) + 1
```

<br>

# Reference

- [Levenshtein Distance youtube](https://www.youtube.com/watch?v=We3YDTzNXEk)
- [Levenshtein Distance](https://python-course.eu/applications-python/levenshtein-distance.php)
