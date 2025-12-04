---
title: "[Algorithm] LeetCode 003: Longest Substring Without Repeating Characters"
description: ""
author: aijinsol
date: 2025-12-04
categories:
  - Algorithm
  - LeetCode
tags:
  - leetcode
  - Sliding-Window
  - Two-Pointers
  - String
  - HashSet
---

> ✏️ [LeetCode 003: Longest Substring Without Repeating Characters](https://leetcode.com/problems/longest-substring-without-repeating-characters/description/)
>
> - Level: Medium
> - Algorithm: Sliding Window / Two Pointers
> - Language: Python3

<br>

## ⚡ 1. Problem

문자열 `s`가 주어졌을 때,

> **중복 없는 가장 긴 부분 문자열의 길이(Longest Substring)**를 구하는 문제.

예:

- `"abcabcbb"` → 3 (`"abc"`)
- `"bbbbb"` → 1 (`"b"`)
- `"pwwkew"` → 3 (`"wke"`)


📍 substring = **연속된 문자열(연속 구간)**  
📍 "중복 없는 최대 길이"가 핵심

<br>

## ⚡ 2. Approach

제약 조건:

```
0 <= len(s) <= 5 * 10^4
```

**O(n^2) Bruteforce 불가**.  
따라서 필수적으로 **O(n) Sliding Window** 풀이가 필요.

> **Sliding Window (Two Pointers)**  
> → 중복 없는 윈도우를 유지하면서 right 확장  
> → 중복 생기면 left를 이동하며 제거  

핵심 전략:

1. `right` 포인터로 한 글자씩 확장
2. `set`으로 현재 윈도우 안의 문자 관리
3. 중복 발생 시 `left` 이동하며 제거
4. 매 순간 윈도우 길이 갱신

<br>

## ⚡ 3. Solution (Sliding Window)

```python
class Solution:
    def lengthOfLongestSubstring(self, s: str) -> int:
        char_set = set()
        l = 0
        res = 0

        for r in range(len(s)):
            while s[r] in char_set:
                char_set.remove(s[l])
                l += 1
            char_set.add(s[r])
            res = max(res, r - l + 1)
        return res
 ```

## ⚡ 4. Time Complexity

- 각 문자는 윈도우에 **최대 1번 들어가고 1번 빠짐**
- `left`, `right` 모두 최대 n번 이동

➡️ O(n)

<br>

## ⚡ 5. Pitfalls

- substring은 **반드시 연속**
- 중복이 완전히 제거될 때까지 `left`를 이동해야 함 → `while` 필수
- set에 중복이 남아 있는 상태에서 추가하면 안 됨

<br>

## ⚡ 6. Related Patterns

**Sliding Window 핵심 패턴** 문제들:

- [[LeetCode: 209] Minimum Size Subarray Sum](https://leetcode.com/problems/minimum-size-subarray-sum/description/)
- [[LeetCode: 904] Fruit Into Baskets](https://leetcode.com/problems/fruit-into-baskets/description/)
- [[LeetCode: 567] Permutation in String](https://leetcode.com/problems/permutation-in-string/description/)
- [[LeetCode: 76] Minimum Window Substring](https://leetcode.com/problems/minimum-window-substring/description/)

📍 떠올릴 키워드:
> **Window / Two Pointers / Expand & Shrink / Set / Frequency**

<br>

## ⚡ 7. Conclusion

이 문제는 **슬라이딩 윈도우 입문 대표 문제**.

핵심:

> **중복 없는 윈도우 유지 → right 확장 → 중복이면 left 이동 → 최대 길이 갱신**

### Always remember:

- Sliding Window: **연속 구간**
- 중복 체크: **set**
- 시간복잡도: **O(n)**
