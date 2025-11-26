---
title: "[Algorithm] LeetCode 001: Two Sum"
description: ""
author: aijinsol
date: 2025-11-26
categories:
  - Algorithm
  - LeetCode
tags:
  - leetcode
  - HashMap
  - Complement
  - One-pass
---

> ✏️ [LeetCode 001: Two Sum](https://leetcode.com/problems/two-sum/description/)
>
> - Level: Easy
> - Algorithm: HashMap
> - Language: Python3

<br>

# 1. ⚡️ Problem

Given an integer array `nums` and an integer `target`,  
return **the indices of the two numbers** such that they add up to `target`.

배열에서 두 숫자를 더해 **target이 되는 정확한 한 쌍의 인덱스**를 반환하는 문제.

Two Sum 문제는 **반드시 하나의 정답이 존재**한다는 조건이 중요.

<br>

# 2. ⚡️ Approach

## 1) Best Practice (Past-Oriented)

- 새로운 숫자 `value`가 들어오면
  **"너의 짝(complement = target - value)이 과거에 있었니?"**
  → 있다면 바로 정답
  → 없다면 `index_map[value] = index` 로 기록

📍 즉, **"지금까지 본 숫자 목록"을 dict에 관리하며 즉시 lookup하는 패턴**
📍 중복⋅변형⋅확장 문제에서도 그대로 재사용 가능한 표준 패턴

<br>

## 2) My First Attempt (Future-Oriented)
 
- 현재 숫자를 보고 **앞으로 필요한 숫자(complement)**를 key로 저장
- 나중에 complement가 들어오면 정답을 찾는 방식

Two Sum에서는 동작하지만
- complement 충돌
- key 중복
- 확장/변형 문제에서 불리

📍 결론: **Past-oriented 패턴이 더 안전하고 재사용성이 높다.**

<br>

# 3. ⚡ Code (Best Practice)

```python
class Solution:
    def twoSum(self, nums: list[int], target: int) -> list[int]:
        index_map = {}  # value -> index
        
        for i, value in enumerate(nums):
            complement = target - value
            if complement in index_map:
                return [index_map[complement], i]
        index_map[value] = i
 ```

<br>

- Simulation
  > **Input**: nums = [2,7,11,15], target = 9
  > **Output**: [0,1]
  - i=0 → val=2 → complement=7 없음 → `{2:0}`
  - i=1 → val=7 → complement=2 있음 → return `[0,1]`

<br>

# 4. ⚡ Pitfalls

- Future-oriented 방식은 complement를 key로 저장해서 확장성 낮음
- 이 문제는 정답 1개 보장, 그래서 "찾자마자 return" 패턴 성립
- `index_map[value] = index` 구조가 가장 안전한 정석 패턴

<br>

# 5. ⚡ Related Patterns

> TwoSum과 구조가 그대로 반복되는 문제들
> (내부적으로 **complement lookup** 을 사용함)

- [Two Sum II](https://leetcode.com/problems/two-sum-ii-input-array-is-sorted/description/) - 정렬된 배열 버전
- [3Sum](https://leetcode.com/problems/3sum/description/) / [4Sum](https://leetcode.com/problems/4sum/description/) -내부 단계가 TwoSum
- [Subarray Sum == K](https://leetcode.com/problems/subarray-sum-equals-k/description/) - `complement = prefix_sum - k`
- [Contains Duplicate II](https://leetcode.com/problems/contains-duplicate-ii/description/) - value → index 기반 거리 체크

📍 이런 문제 나오면 **"HashMap + complement lookup" 패턴을 가장 먼저 떠올릴 것!**

<br>

# 6. ⚡ Conclusion

- 앞으로 아래 유형이 나오면 → **TwoSum 패턴으로 접근!**
  - 배열에서 두 값/조건으로 특정 쌍 찾기
  - 과거 값과 현재 값의 관계로 정답 판단
  - 어떤 값이 과거에 있었는지 확인하는 구조
  - sum / difference / index-distance 기반 문제
  - TwoSum이 내부에 포함된 조합 문제(3Sum⋅4Sum)

### Always remember:

  - `HashMap`
  - `value -> index`
  - `complement`
  - `One-pass`
  - past-oriented lookup
