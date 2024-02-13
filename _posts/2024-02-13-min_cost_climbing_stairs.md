---
title: "[Algorithm] Leetcode #746. Min Cost Climbing Stairs"
excerpt: "Climbing Stairs Series #2"
categories:
  - Algorithm
tags:
  - recursive
  - dp
  - cache
last_modified_at: 2024-02-13
---

> ✏️ [LeetCode #746. Min Cost Climbing Stairs](https://leetcode.com/problems/min-cost-climbing-stairs/)

# Intro

Leetcode의 Min Cost Climbing Stairs 문제를 4가지 방식(재귀, Top-Down DP, Bottom-Up DP, Cache)으로 풀어보자.

<br>

# Solution 1) Recursive

> Time Complexity: O(2<sup>n</sup>)

아래 코드에서는 클래스 `Solution` 내에 `minCostClimbingStairs` 메서드가 정의되어 있으며, 여기서 중첩함수 `recur`를 사용하여 재귀적으로 최소 비용을 계산한다.

```python
class Solution:
    def minCostClimbingStairs(self, cost: list[int]) -> int:
        n = len(cost)

        def recur(n):
            if n <= 1:
                return 0

            return min(recur(n - 1) + cost[n - 1], recur(n - 2) + cost[n - 2])

        return recur(n)
```

<br>

이번엔 중첩함수를 쓰지 않고 메소드를 분리해서 코드를 짜보자!

```python
class Solution:
    def minCostClimbingStairs(self, cost: list[int]) -> int:
        self.cost = cost
        n = len(cost)

        return self.recur(n)

    def recur(self, n: int) -> int:
        if n <= 1:
            return 0

        return min(self.recur(n - 1) + self.cost[n - 1], self.recur(n - 2) + self.cost[n - 2])
```

위 코드에서는 중첩함수 대신에 클래스의 또 다른 메서드인 `recur`를 분리하였다. 이 방식은 재귀 호출을 통해 동일한 계산을 수행하지만, `recur` 메서드가 클래스 내부의 다른 메서드로 분리되어 있어서 코드의 가독성과 구조가 개선되었다!

<br>

# Solution 2) DP: Top-Down (memoization)

> Time Complexity: O(n)

```python
class Solution:
    def minCostClimbingStairs(self, cost: list[int]) -> int:
        self.cost = cost
        n = len(cost)

        return self.dp_top_down(n)

    def dp_top_down(self, n: int, dp: dict[int, int] | None = None) -> int:
        if dp is None:
            dp = {0: 0, 1: 0}

        if n not in dp:
            dp[n] = min(
                self.dp_top_down(n - 1) + self.cost[n - 1],
                self.dp_top_down(n - 2) + self.cost[n - 2],
            )

        return dp[n]
```

<br>

# Solution 3) DP: Bottom-Up (for loop)

> Time Complexity: O(n)

## `List` 활용

```python
class Solution:
    def minCostClimbingStairs(self, cost: list[int]) -> int:
        n = len(cost)

        if n <= 1:
            return 0

        dp = [0] * (n + 1)
        dp[0], dp[1] = 0, 0

        for idx in range(2, n + 1):
            dp[idx] = min(dp[idx - 1] + cost[idx - 1], dp[idx - 2] + cost[idx - 2])

        return dp[n]
```

## `Dictionary` 활용

```python
class Solution:
    def minCostClimbingStairs(self, cost: list[int]) -> int:
        n = len(cost)
        dp = {0: 0, 1: 0}

        if n <= 1:
            return dp[n]

        for key in range(2, n + 1):
            dp[key] = min(dp[key - 1] + cost[key - 1], dp[key - 2] + cost[key - 2])

        return dp[n]
```

<br>

# Solution 4) `functools.cache`

> Time Complexity: O(n)

```python
from functools import cache


@cache
class Solution:
    def minCostClimbingStairs(self, cost: list[int]) -> int:
        n = len(cost)

        def recur(n):
            if n <= 1:
                return 0

            return min(recur(n - 1) + cost[n - 1], recur(n - 2) + cost[n - 2])

        return recur(n)
```
