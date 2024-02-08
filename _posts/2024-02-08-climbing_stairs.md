---
title: "[Algorithm] Leetcode #70. Climbing Stairs"
excerpt: "재귀함수, dp, cache 3가지 방법으로 풀어보기"
categories:
  - Algorithm
tags:
  - recursive
  - dp
  - cache
last_modified_at: 2024-02-08
---

> ✏️ [LeetCode #70. Climbing Stairs](https://leetcode.com/problems/climbing-stairs/)

# Intro

[[Algorithm] 피보나치 수열 구현하기](https://aijinsol.github.io/algorithm/fibonacci/) 포스팅에서 피보나치 수열을 4가지 방식(재귀, Top-Down DP, Bottom-Up DP, Cache)으로 구현해 봤다. 이번 포스팅에서는 Leetcode의 Climbing Stairs 문제를 이 4가지 방식으로 풀어보자.

<br>

# Solution 1) Recursive

> Time Complexity: O(2<sup>n</sup>)

```python
def climbStairs_recursive(n: int) -> int:
    if n <= 2:
        return n
    return climbStairs_recursive(n - 1) + climbStairs_recursive(n - 2)
```

<br>

# Solution 2) DP: Top-Down (memoization)

> Time Complexity: O(n)

```python
from typing import Dict


def climbStairs_top_down(n: int, memo: Dict[int, int] = None) -> int:
    if memo is None:
      memo = {1: 1, 2: 2}
    if n in memo:
        return memo[n]
    memo[n] = climbStairs_top_down(n - 1, memo) + climbStairs_top_down(n - 2, memo)
    return memo[n]
```

<br>

# Solution 3) DP: Bottom-Up (for-loop)

> Time Complexity: O(n)

```python
def climbStairs_bottom_up(n: int) -> int:
    if n <= 2:
        return n
    prev_two_steps, prev_one_step = 1, 2
    for i in range(3, n + 1):
        current = prev_one_step + prev_two_steps
        prev_two_steps, prev_one_step = prev_one_step, current
    return prev_one_step
```

<br>

# Solution 4) `functools.cache`

> Time Complexity: O(n)

```python
from functools import cache


@cache
def climbStairs_cache(n: int) -> int:
    if n <= 2:
        return n
    return climbStairs_cache(n - 1) + climbStairs_cache(n - 2)
```
