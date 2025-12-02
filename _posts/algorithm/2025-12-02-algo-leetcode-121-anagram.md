---
title: "[Algorithm] LeetCode 242: Valid Anagram"
description: ""
author: aijinsol
date: 2025-12-02
categories:
  - Algorithm
  - LeetCode
tags:
  - leetcode
  - HashMap
  - Sorting
  - Anagram
  - String
---

> ✏️ [LeetCode 242: Valid Anagram](https://leetcode.com/problems/valid-anagram/description/)
>
> - Level: Easy
> - Algorithm: Sorting / HashMap
> - Language: Python3

<br>

## ⚡ 1. Problem

두 문자열 `s`, `t`가 주어졌을 때,

> **서로 글자 구성(문자 → 개수)이 완전히 동일하면**  
> 두 문자열은 *anagram*

예:

- `"anagram"` ↔ `"nagaram"` → True
- `"rat"` ↔ `"car"` → False

📍 **문자 순서는 상관 없음.**  
📍 **"문자 종류 + 각 문자 개수"가 동일한지를 판단하는 문제.**

<br>

## ⚡ 2. Approach

입력 제약:

```
1 <= s.length, t.length <= 5 * 10^4
```

📍 **n = max(len(s), len(t))**

- 가능한 시간복잡도: **O(n), O(nlogn)**
- 문자열 비교 문제의 전형적인 두 패턴:  
  1) **정렬(Sort)** → O(nlogn)  
  2) **해시맵(dict / Counter)** → O(n)

두 방식 모두 통과 가능하지만,
정렬 방식이 가장 직관적이며 코드가 짧다.

<br>

## ⚡ 3. Naive Approach (Sort)

### Code (Sort)

```python
class Solution:
    def isAnagram(self, s: str, t: str) -> bool:
        if sorted(s) == sorted(t):
            return True
        return False
 ```

### Time Complexity

- 정렬: O(nlogn)
- 비교: O(n)

➡️ 총 O(nlogn)

<br>

## ⚡ 4. Best Practice: HashMap (Counter)

정렬 없이, **문자 개수를 세어서 비교**하는 방식.  
문자열 기반 문제에서 가장 효율적인 풀이.

### Code (Counter)

```python
from collections import Counter


class Solution:
    def isAnagram(self, s: str, t: str) -> bool:
        return Counter(s) == Counter(t)
```

### Time Complexity

- Counting: O(n)
- Dictionary 비교: O(n)

➡️ 총 O(n)

<br>

## ⚡ 5. Pitfalls

- 문자 순서는 중요하지 않지만 **문자 개수**는 정확해야 한다.
- 문자열 길이가 다르면 바로 False.

<br>

## ⚡ 7. Related Patterns

아래 문제들은 공통적으로  
**문자의 빈도(Counting) 또는 해시맵 비교** 패턴 사용


- [[LeetCode: 383] Ransom Note](https://leetcode.com/problems/maximum-difference-between-increasing-elements/description/)
- [[LeetCode: 49] Group Anagrams](https://leetcode.com/problems/best-time-to-buy-and-sell-stock-ii/description/)
- 문자열 빈도수 기반 문제 전반

📍 떠올릴 키워드:
> **Sorting / Counting / HashMap / Character Frequency**

<br>

## ⚡ 8. Conclusion

문자열 anagram 문제는 결국 **문자 종류와 개수가 같은지** 확인하는 문제이며,  
대표적인 해결 방식은 2가지.

- **Sorting → O(nlogn)**
- **Counter(빈도수 세기) → O(n)**

### Always remember:

  - 문자열 비교 = **Sort** 또는 **Counter**
  - 핵심은 **문자 개수 동일 여부**
