---
title: "[Algorithm] LeetCode 438: Find All Anagrams in a String"
description: ""
author: aijinsol
date: 2025-12-12
categories:
  - Algorithm
  - LeetCode
tags:
  - leetcode
  - Sliding-Window
  - Two-Pointers
  - String
  - Counter
---

> ✏️ [LeetCode 438: Find All Anagrams in a String](https://leetcode.com/problems/find-all-anagrams-in-a-string/description/)
>
> - Level: Medium
> - Algorithm: Sliding Window / Frequency Count
> - Language: Python3

<br>

## ⚡ 1. Problem

두 문자열 `s`, `p`가 주어졌을 때,

> **s 안에서 p의 anagram이 되는 모든 시작 index를 찾는 문제**

예:

- `s = "cbaebabacd"`, `p` = "abc"  
  → 결과: `[0, 6]`  
  (`"cba"`, `"bac"`이 `abc`의 anagram)

📍 **substring = 연속된 구간**  
📍 **anagram = 글자 종류 + 개수가 동일한 문자열**

<br>

## ⚡ 2. Approach

제약 조건:

```
1 <= len(s), len(p) <= 3 * 10^4
```

Bruteforce는 O(n*m) 이상 → **시간 초과 확정**  
따라서 필수적으로 **O(n) Sliding Window**가 필요.

### Sliding Window 풀이법

> ⭐ 공통 패턴이니 암기!!

문자열 `p`의 길이를 `m`이라고 하면,

1. **초기 윈도우** 구축
- s[0:m] 구간의 빈도수를 미리 계산
- p의 빈도수와 비교

2. 이후 **윈도우 이동 반복**
- 오른쪽 문자 1개 추가
- 왼쪽 문자 1개 제거
- 빈도수가 동일하면 → anagram 발견

📍 Window는 **항상 m 크기를 유지**  
📍 각 문자는 한 번 들어오고 한 번 빠짐 → O(n)

<br>

## ⚡ 3. Solution (Sliding Window)

```python
from collections import Counter


class Solution:
    def findAnagrams(self, s: str, p: str) -> list[int]:
        if len(p) > len(s):
          return []

        p_cnt = Counter()  # p의 문자 빈도
        s_cnt = Counter()  # 초기 window(0 ~ m-1)

        res = [0] if s_cnt == p_cnt else []   # 초기 window 비교

        l = 0
        for r in range(len(p), len(s)):       # r: 새로 들어오는 문자
          s_cnt[s[r]] += 1                    # 오른쪽 문자 추가
          s_cnt[s[l]] -= 1                    # 왼쪽 문자 제거

          if s_cnt[s[l]] == 0:                # 빈도 0이면 dict에서 삭제
            del s_cnt[s[l]]

          l += 1                              # window 이동

          if s_cnt == p_cnt:                  # anagram 발견
            res.append(l)

        return res
 ```

## ⚡ 4. Time Complexity

- window 이동: 전체 문자열을 한 번만 훑음
- 각 문자는 **window에 1번 들어오고 1번 빠짐**

➡️ O(n)

<br>

## ⚡ 5. Pitfalls

- substring은 **반드시 연속**
- window 크기 = len(p)를 **항상 유지**해야 함
- Counter에서 빈도 0일 때 **키 삭제해야 동일 비교 가능**
- 초기 window와 sliding을 **두 단계로 나누는 패턴 필수**

<br>

## ⚡ 6. Related Patterns

**Sliding Window 핵심 패턴** 문제들:

- [[LeetCode: 209] Minimum Size Subarray Sum](https://leetcode.com/problems/minimum-size-subarray-sum/description/)
- [[LeetCode: 567] Permutation in String](https://leetcode.com/problems/permutation-in-string/description/)
- [[LeetCode: 76] Minimum Window Substring](https://leetcode.com/problems/minimum-window-substring/description/)
- [[LeetCode 003] Longest Substring Without Repeating Characters](https://leetcode.com/problems/longest-substring-without-repeating-characters/description/)
  - [✏️ [LeetCode 003] 포스팅](https://aijinsol.github.io/posts/algo-leetcode-003-logest-substring/)

📍 떠올릴 키워드:
> **Sliding Window / Two Pointers / Frequency Count / Fixed-size Window**

<br>

## ⚡ 7. Conclusion

이 문제는 **고정 길이(Fixed-size) 슬라이딩 윈도우**의 정석 문제.

핵심:

> **1) 초기 윈도우 빈도 계산**  
> **2) 오른쪽 추가 + 왼쪽 제거로 창을 한 칸씩 이동**  
> **3) p의 빈도와 같으면 시작 인덱스 기록**

### Always remember:

- Sliding Window: 초기 윈도우 + 슬라이딩 단계
- 빈도 비교: **Counter** 또는 **dict**
- 시간복잡도: **O(n)**
