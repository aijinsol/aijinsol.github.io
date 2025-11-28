---
title: "[Algorithm] LeetCode 121: Best Time to Buy and Sell Stock"
description: ""
author: aijinsol
date: 2025-11-28
categories:
  - Algorithm
  - LeetCode
tags:
  - leetcode
  - One-pass
  - Sliding-Min
  - Max-Difference
  - Array
---

> ✏️ [LeetCode 121: Best Time to Buy and Sell Stock](https://leetcode.com/problems/best-time-to-buy-and-sell-stock/description/)
>
> - Level: Easy
> - Algorithm: One-pass
> - Language: Python3

<br>

## ⚡ 1. Problem

배열 `prices[i]`는 **i번째 날의 주식 가격**을 의미한다.

- 하루 한 번만 *구매(buy)*
- 그 이후 어느 날 *판매(sell)*
- `판매가 - 구매가` 최대값을 반환
- 이익이 없으면 `0` 반환

즉,

> **미래 가격 - 과거  최저 가격**
> 의 최댓값을 찾는 문제.

<br>

## ⚡ 2. Approach

입력 제약:

```
1 <= prices.length <= 10^5
```

📍 **n = prices.length**

- n <= 10^5
  - 가능한 시간복잡도: **O(n), O(nlogn)**
  - ❌ O(n^2) 불가능 (10^10 연산 → TLE)

따라서 이 문제는 **단일 스캔(one-pass)**으로 해결해야 한다.

<br>

## ⚡ 3. Naive Approach (Bruteforce)

```python
for i in range(n):
  for j in range(i+1, n):
    profit = prices[j] - prices[i]
 ```

위 구조는 이중 for문 → `O(n^2)`  

반복 횟수는  
`(n-1) + (n-2) + ... + 1 = n(n-1)/2 ~= 1/2 × n^2`  

즉, **약 절반의 n^2** 수준이다.

n = 10^5 라면:

- 1/2 × (10^5)^2 = 5 × 10^9 회
- 1초 = 10^8 연산 기준 → 최소 50초
- → **즉시 TLE (Time Limit Exceeded)**

➡️ 완전탐색은 절대 통과 불가

<br>

## ⚡ 4. Best Practice: One-pass (Sliding Minimum)

이 문제는 **배열을 한 번만 순회(One-pass)하면서**  
현재까지의 **최저가(Sliding Minimum)**을 유지하고,  
오늘 팔면 얻는 이익을 계산해 최대값을 찾는 방식으로 해결한다.

핵심 아이디어:

> - `min_price`: 지금까지 본 가격 과거중 가장 낮은 값
> - `max_profit`: 지금까지 계산된 최대 이익
> - 배열을 한 번만 순회 → **O(n)**

📍 전형적인 **Max Difference from Left** 패턴.

<br>

### Code (One-pass)

```python
class Solution:
    def maxProfit(self, prices: list[int]) -> int:
        min_price = float("inf")
        max_profit = 0

        for price in prices:
            if price < min_price:
                min_price = price  # 최저가 갱신
            else:
                profit = price - min_price
                if profit > max_profit:
                    max_profit = profit  # 최대 이익 갱신

        return max_profit
```

<br>

### Simulation

Input: `[7,1,5,3,6,4]`

| Day | Price | min_price | profit | max_profit |
| --- | ----- | --------- | ------ | ---------- |
| 1   | 7     | 7         | -      | 0          |
| 2   | 1     | 1         | -      | 0          |
| 3   | 5     | 1         | 4      | 4          |
| 4   | 3     | 1         | 2      | 4          |
| 5   | 6     | 1         | **5**  | **5**      |
| 6   | 4     | 1         | 3      | 5          |

→ Output: `5`

<br>

## ⚡ 6. Pitfalls

- n <= 10^5 → O(n^2) 구조 절대 불가
- `min_price`는 반드시 **과거 값**이어야 함
- 최저가 갱신을 놓치면 잘못된 결과

<br>

## ⚡ 7. Related Patterns

아래 문제들은 공통적으로  
**"과거 최소값/최대값 유지 + 현재 값으로 계산"** 패턴 사용


- [[LeetCode: 20162] Maximum Difference Between Increasing Elements](https://leetcode.com/problems/maximum-difference-between-increasing-elements/description/)
- [[LeetCode: 122] Best Time to Buy and Sell Stock II](https://leetcode.com/problems/best-time-to-buy-and-sell-stock-ii/description/)
- [[LeetCode: 123] Best Time to Buy and Sell Stock III](https://leetcode.com/problems/best-time-to-buy-and-sell-stock-iii/description/)
- [[LeetCode: 188] Best Time to Buy and Sell Stock IV](https://leetcode.com/problems/best-time-to-buy-and-sell-stock-iv/description/)
- [[LeetCode: 739] Daily Temperatures](https://leetcode.com/problems/daily-temperatures/description/)
- [[LeetCode: 417] Pacific Atlantic Water Flow](https://leetcode.com/problems/pacific-atlantic-water-flow/description/)
- [[LeetCode: 53] Maximum Subarray](https://leetcode.com/problems/maximum-subarray/description/) (Kadane Algorithm)
- 시계열 기반 차이 계산 문제 전반 (주가/습도/전력량/센서 데이터 등)

📍 떠올릴 키워드:
> **min_price → profit = price - min_price → max_profit**  
> **One-pass / Sliding Minimum / O(n)**

<br>

## ⚡ 8. Conclusion

- 제약조건: n <= 10^5 → O(n^2) 금지
- 최적 솔루션 = O(n) 단일 스캔(one-pass)
- 핵심은
  - 과거 최저가 유지
  - 현재 이익 계산
  - 최대 이익 갱신

### Always remember:

  - `min_price`
  - `max_profit`
  - `One-pass`
  - `max(price - min_price)`
  - `Sliding minimum`
