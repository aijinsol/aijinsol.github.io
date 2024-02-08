---
title: "[Algorithm] 피보나치 수열 구현하기"
excerpt: "재귀함수, top-down dp, bottom-up, cache 4가지 방법으로 구현하기"
categories:
  - Algorithm
tags:
  - recursive
  - cache
  - dp
last_modified_at: 2024-02-08
---

# Intro

알고리즘 공부할 때 기본이 되는 피보나치 수열은 다양한 프로그래밍 개념을 공부하기에 아주 좋은 예제이다. 이번 포스팅에서는 기본 재귀, Dynamic Programming의 Top-Down과 Bottom-Up 그리고 Python 3.9 이상에서 사용 가능한 `functools.cache`를 이용한 네 가지 다른 접근 방법으로 피보나치 수열을 구현하는 방법을 비교해볼 것이다. 실행 시간, 메모리 사용량 및 내부 메커니즘을 기준으로 이러한 방법들을 비교하며, 단순하지만 비효율적인 방법에서 더 발전된 효율적인 솔루션을 구현해보자! 😀

<br>

# Solution 1) Recursive

> Time Complexity: O(2<sup>n</sup>)

```python
def fibonacci_recursive(n: int) -> int:
    if n < 2:
        return n
    return fibonacci_recursive(n - 1) + fibonacci_recursive(n - 2)
```

```python
# 실행 시간 측정
import time


start_time_recursive = time.time()
print(fibonacci_recursive(30))
end_time_recursive = time.time()

print(f"With recursive: {end_time_recursive - start_time_recursive} seconds")
```

```
832040
With recursive: 0.18014598 seconds
```

- O(2<sup>n</sup>)의 높은 지수적 시간복잡도라는 아쉬움!
- 각 함수 호출은 2개의 추가 호출을 생성하므로, 방대한 수의 중복 계산이 발생한다.
- 큰 `n`에 대해 호출 횟수가 폭발적으로 증가하며, 실행시간과 stack 공간 사용량이 증가하는 주요 단점이 있다.

<br>

# Solution 2) DP: Top-Down (memoization)

- 재귀함수의 한계를 극복하기 위해 memoization을 적용한 DP를 사용할 수 있다. 각 계산의 결과를 저장함으로써 중복 작업을 피한다.

> Time Complexity: O(n)

```python
from typing import Dict


def fibonacci_top_down(n: int, memo: Dict[int, int] = None) -> int:
    if memo is None:
        memo = {0: 0, 1: 1}  # Base cases
    if n in memo:
        return memo[n]
    memo[n] = fibonacci_top_down(n - 1, memo) + fibonacci_top_down(n - 2, memo)
    return memo[n]
```

```python
# 실행 시간 측정
start_time_top_down = time.time()
print(fibonacci_top_down(30))
end_time_top_down = time.time()

print(f"With top_down: {end_time_top_down - start_time_top_down} seconds")
```

```
832040
With top_down: 0.00006318 seconds
```

- 실행시간을 선형 복잡도인 O(n)으로 크게 줄이며, memoization dictionary에 대한 메모리 사용량이 소폭 증가한다.
- 더 큰 피보나치 수를 효율적으로 계산한다.

<br>

# Solution 3) DP: Bottom-Up (for-loop)

> Time Complexity: O(n)

```python
def fibonacci_bottom_up(n: int) -> int:
    if n <= 1:
        return n
    prev_two, prev_one = 0, 1
    for i in range(2, n + 1):
        prev_two, prev_one = prev_one, prev_one + prev_two
    return prev_one
```

```python
# 실행 시간 측정
start_time_bottom_up = time.time()
print(fibonacci_bottom_up(30))
end_time_bottom_up = time.time()

print(f"With bottom_up: {end_time_bottom_up - start_time_bottom_up} seconds")
```

```
832040
With bottom_up: 0.00004315 seconds
```

<br>

# Solution 4) `functools.cache`

> Time Complexity: O(n)

```python
from functools import cache


@cache
def fibonacci_cache(n: int) -> int:
    if n < 2:
        return n
    return fibonacci_cache(n - 1) + fibonacci_cache(n - 2)
```

```python
# 실행 시간 측정
start_time_cache = time.time()
print(fibonacci_cache(30))
end_time_cache = time.time()

print(f"With cache: {end_time_cache - start_time_cache} seconds")
```

```
832040
With cache: 0.00007820 seconds
```

- Top-Down DP 접근 방식과 마찬가지
- `functools.cache`는 실행시간을 O(n)으로 줄이면서 캐싱 메커니즘을 내부적으로 처리한다.
- 이는 성능을 개선할 뿐만 아니라 memoization 과정을 추상화하여 코드를 단순화한다.

<br>

# Conclusion

📊 4가지 구현 방식 성능 비교!

## 1) Time Complexity & Runtime

> Recursive > Cache > Top-Down > Bottom-Up

- <b>Recursive</b>
  - Time Complexity: O(2<sup>n</sup>)
  - 기본 재귀 방식은 매우 비효율적이다. 각 단계에서 이미 계산된 값을 재계산하기 때문에, 시간복잡도가 O(2<sup>n</sup>)이다. 따라서 실행시간이 매우 길게 나온다.
- <b>`functools.cache`</b>
  - Time Complexity: O(n)
  - 결과를 캐시하여 중복 계산을 방지한다. 이로 인해 시간복잡도가 O(n)으로 줄어들어 재귀 방식에 비해 크게 개선된 성능을 보인다.
- <b>Top-Down DP</b>
  - Time Complexity: O(n)
  - Memoization을 사용하여 중복 계산을 피하므로, 재귀 방식보다는 훨씬 빠르다. 그러나 재귀 호출의 overhead로 인해 Bottom-Up DP 방식보다는 약간 느리다.
- <b>Bottom-Up DP</b>
  - Time Complexity: O(n)
  - 처음부터 순차적으로 문제를 해결하는 방식! 중간 결과를 저장하는 추가 메모리를 사용하지 않고, 함수 호출 stack이 증가하는 overhead가 없다. 모든 계산이 이전 계산 결과에 기반하기 때문에 CPU 캐시 효율성이 높고, 이러한 이유들로 가장 빠른 실행 시간을 달성한다!

## 2) Memory Usage

> Recursive > Cache ~= Top-Down DP > Bottom-Up DP

- <b>Recursive</b>
  - 메모리 사용량: 매우 높음
  - 이유: 각 재귀 호출마다 함수의 실행 컨텍스트가 스택 메모리에 새로 생성되므로, 깊은 재귀가 발생하면 stack 메모리 사용량이 급격히 증가한다. 특히 피보나치 수열에서는 같은 값을 계산하기 위해 수많은 중복 호출이 발생하므로 stack overflow가 발생할 수도 있다.
- <b>`functools.cache`</b>
  - 메모리 사용량: 적당
  - 이유: 각 함수 호출의 결과가 캐시에 저장되므로 중복 계산을 피할 수 있다. 그러나 캐시를 위한 추가 메모리가 필요하고, Python의 재귀 호출 stack에도 여전히 의존한다.
- <b>Top-Down DP</b>
  - 메모리 사용량: 적당
  - 이유: `functools.cache`를 사용하는 방식과 유사하게 중간 계산 결과를 저장한다. Memoization을 위한 메모리가 필요하지만, 재귀 호출이 최적화되어 있어 `functools.cache`보다는 약간 적은 메모리를 사용할 수 있다.
- <b>Bottom-Up DP</b>
  - 메모리 사용량: 가장 낮음
  - 이유: 재귀 호출을 사용하지 않으며, 오직 계산에 필요한 최소한의 변수만을 사용하여 값을 갱신한다. 즉, 고정된 수의 변수만을 사용하고 추가적인 메모리 할당이 필요 없으므로 메모리 효율성이 가장 높다.

## 3) Ease of Use

> Cache > Bottom-Up DP > Top-Down DP > Recursive

- <b>Recursive</b>
  - 사용 용이성: 낮음
  - 이유: 가장 기본적이고 직관적인 접근 방식이긴 하지만, 큰 n 값에 대해서는 매우 비효율적이며 실행 시간이 길어진다. 또한, stack overflow의 위험이 있어 실제 사용에서는 제한적이다.
- <b>`functools.cache`</b>
  - 사용 용이성: 높음
  - 이유: Python 3.9 이상에서 사용할 수 있는 `functools.cache` 데코레이터를 사용하여 간단히 memoization을 적용할 수 있다. 기존 재귀 함수에 데코레이터 한 줄을 추가하는 것만으로 성능을 대폭 개선할 수 있으므로, 사용 용이성 측면에서 매우 효과적!
- <b>Top-Down DP</b>
  - 사용 용이성: 중간
  - 이유: Memoization을 수동으로 구현해야 하므로 `functools.cache`를 사용하는 것보다는 약간 복잡할 수 있다. 하지만, 재귀 함수를 사용하므로 여전히 구현이 상대적으로 직관적. Memoization 로직을 직접 관리해야 하는 부담이 있긴 하지만, 특정 문제에 대해 더 세밀한 최적화가 가능하다.
- <b>Bottom-Up DP</b>
  - 사용 용이성: 중간 ~ 높음
  - 이유: 초기값 설정 후 반복문을 통해 해를 구축해 나가는 방식은 직관적이고 구현이 단순하다. 전체 문제를 더 작은 하위 문제로 나누고 이를 순차적으로 해결하는 방식을 이해해야 한다. 메모리와 성능 측면에서 우수하며 stack overflow 위험이 없어 큰 데이터셋에도 안정적으로 사용할 수 있다.
