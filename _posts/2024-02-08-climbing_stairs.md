---
title: "[Algorithm] Leetcode #70. Climbing Stairs"
excerpt: "재귀함수, top-down dp, bottom-up, cache 4가지 방법으로 풀어보기"
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
def climbStairs_top_down(n: int, dp: dict[int, int] | None = None) -> int:
    if dp is None:
      dp = {1: 1, 2: 2}

    if n not in dp:
        dp[n] = climbStairs_top_down(n - 1, dp) + climbStairs_top_down(n - 2, dp)

    return dp[n]
```

<br>

# Solution 3) DP: Bottom-Up (for loop)

> Time Complexity: O(n)

## `List` 활용

```python
def climbStairs_bottom_up_list(n: int) -> int:
    if n <= 2:
        return n

    dp = [0] * (n + 1)
    dp[1], dp[2] = 1, 2

    for i in range(3, n + 1):
        dp[i] = dp[i - 1] + dp[i - 2]

    return dp[n]
```

## `Dictionary` 활용

```python
def climbStairs_bottom_up_dict(n: int) -> int:
    dp = {1: 1, 2: 2}

    if n <= 2:
        return dp[n]

    for i in range(3, n + 1):
        dp[i] = dp[i - 1] + dp[i - 2]

    return dp[n]
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
