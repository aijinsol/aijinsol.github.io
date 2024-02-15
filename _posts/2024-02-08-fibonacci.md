---
title: "[Algorithm] DP Problems Series #1: 피보나치 수열 구현하기"
excerpt: "재귀함수, top-down dp, bottom-up dp, cache 4가지 방법으로 구현하기"
categories:
  - Algorithm
tags:
  - recursive
  - cache
  - dp
last_modified_at: 2024-02-08
---

> <b>DP Problems Series</b>에서는 DP 문제를 접했을 때 풀이할 수 있는 4가지 방법으로 정리하고 풀어본다.
>
> - [[Algorithm] DP Problems Series #1: 피보나치 수열 구현하기](https://aijinsol.github.io/algorithm/fibonacci/)
> - [[Algorithm] DP Problems Series #2: Leetcode #70. Climbing Stairs](https://aijinsol.github.io/algorithm/climbing_stairs/)
> - [[Algorithm] DP Problems Series #3: Leetcode #746. Min Cost Climbing Stairs](https://aijinsol.github.io/algorithm/min_cost_climbing_stairs/)

# Intro

알고리즘 공부할 때 기본이 되는 피보나치 수열은 다양한 프로그래밍 개념을 공부하기에 아주 좋은 예제이다. 이번 포스팅에서는 <b>1) 기본 재귀</b>, <b>2) Dynamic Programming의 Top-Down</b>과 <b>3) Bottom-Up</b> 그리고 Python 3.9 이상에서 사용 가능한 <b>4) `functools.cache`</b>를 이용한 4가지 다른 접근 방법으로 피보나치 수열을 구현하는 방법을 비교해볼 것이다. 실행 시간, 메모리 사용량 및 내부 메커니즘을 기준으로 이러한 방법들을 비교하며, 단순하지만 비효율적인 방법에서 더 발전된 효율적인 솔루션을 구현해보자! 😀

<br>

# Solution 1) Recursive

> Time Complexity: O(2<sup>n</sup>)

```python
def fibonacci_recursive(n: int) -> int:
    if n < 2:
        return n  # Base cases

    return fibonacci_recursive(n - 1) + fibonacci_recursive(n - 2)
```

- O(2<sup>n</sup>)의 높은 지수적 시간복잡도라는 아쉬움!
- 각 함수 호출은 2개의 추가 호출을 생성하므로, 방대한 수의 중복 계산이 발생한다.
- 큰 `n`에 대해 호출 횟수가 폭발적으로 증가하며, 실행시간과 stack 공간 사용량이 증가하는 주요 단점이 있다.

<br>

# Solution 2) DP: Top-Down (memoization)

- 재귀함수의 한계를 극복하기 위해 memoization을 적용한 DP를 사용할 수 있다. 각 계산의 결과를 저장함으로써 중복 작업을 피한다.

> Time Complexity: O(n)

```python
def fibonacci_top_down(n: int, dp: dict[int, int] | None = None) -> int:
    if dp is None:
        dp = {0: 0, 1: 1}  # Base cases

    if n not in dp:
        dp[n] = fibonacci_top_down(n - 1, dp) + fibonacci_top_down(n - 2, dp)

    return dp[n]
```

- 실행시간을 선형 복잡도인 O(n)으로 크게 줄이며, memoization dictionary에 대한 메모리 사용량이 소폭 증가한다.
- 더 큰 피보나치 수를 효율적으로 계산한다.

<br>

# Solution 3) DP: Bottom-Up (for loop)

> Time Complexity: O(n)

## `List` 활용

```python
def fibonacci_bottom_up_list(n: int) -> int:
    dp = [0] * (n + 1)
    dp[0], dp[1] = 0, 1  # Base cases

    if n <= 1:
        return n

    for idx in range(2, n + 1):
        dp[idx] = dp[idx - 1] + dp[idx - 2]

    return dp[n]
```

❓ List를 사용하여 Bottom-Up DP를 구현한 방식의 장단점은 어떻게 될까?

- 장점
  - <b>Index 접근</b>: List는 index를 통해 각 원소에 빠르게 접근할 수 있으므로, 연속적인 숫자를 다룰 때 이 방식이 자연스럽고 효율적이다. 피보나치 수열처럼 연속적인 값을 순차적으로 계산할 때 적합하다!
  - <b>Memory 사용</b>: List는 미리 정의된 크기의 연속된 memory 공간에 데이터를 저장하므로, 피보나치 수열과 같이 연속적인 index에 대해 계산할 때 공간을 효율적으로 사용할 수 있다.
- 단점
  - <b>Fixed size</b>: List를 사용할 때는 계산하고자 하는 피보나치 수열의 최대 index에 해당하는 크기로 미리 memory를 할당해야 하므로, 매우 큰 index 값을 계산할 경우 초기 memory 할당이 비효율적일 수 있다.

<div class="notice--success" markdown="1">
💡 코테에서 웬만하면 Bottom-Up DP는 Dictionary보다는 List로 구현하자!
- List 기반 구현이 일반적으로 index 접근이 빠르기 때문에 약간 더 효율적일 수 있다. Dictionary 기반 구현은 더 유연하고 동적인 데이터 저장이 가능하지만, 이러한 피보나치 수열 문제를 포함한 대부분의 코딩테스트 문제에서는 데이터 범위가 연속적이거나 dense한 데이터 범위를 가지므로 list가 더 적합할 수 있다.
</div>

<br>

## `Dictionary` 활용

```python
def fibonacci_bottom_up_dict(n: int) -> int:
    dp = {0: 0, 1: 1}  # Base cases

    if n <= 1:
        return dp[n]

    for key in range(2, n + 1):
        dp[key] = dp[key - 1] + dp[key - 2]

    return dp[n]
```

❓ Dictionary를 사용하여 Bottom-Up DP를 구현한 방식의 장단점은 어떻게 될까?

- 장점
  - <b>유연한 Memory 사용</b>: Dictionary를 사용하면 필요한 key-value 쌍만 저장하므로, 계산 과정에서 필요한 index의 값만 memory에 유지하게 된다. 이는 계산해야 하는 값의 범위가 클 때 memory 사용을 좀 더 유연하게 관리할 수 있게 한다.
  - <b>Key-value 접근</b>: Dictionary는 key-value 쌍으로 데이터를 저장하므로, 특정 조건에 따라 index가 비연속적인 경우에도 효율적으로 데이터를 관리하고 접근할 수 있다.
- 단점
  - <b>접근 시간</b>: 파이썬의 dictionary는 내부적으로 해시 테이블을 사용하여 구현되므로, 평균적인 경우에는 매우 빠른 접근 시간을 제공한다. 그러나 최악의 경우(해시 collision이 많을 때) 접근 시간이 늘어날 수 있다.
  - <b>Memory overhead</b>: Dictionary는 key와 value 모두를 저장해야 하므로, list에 비해 추가적인 memory overhead가 발생할 수 있다. 특히 key-value 쌍이 많아질수록 이 overhead는 더욱 증가한다.

<br>

# Solution 4) `functools.cache`

> Time Complexity: O(n)

```python
from functools import cache


@cache
def fibonacci_cache(n: int) -> int:
    if n < 2:
        return n  # Base cases

    return fibonacci_cache(n - 1) + fibonacci_cache(n - 2)
```

- Top-Down DP 접근 방식과 마찬가지
- `functools.cache`는 실행시간을 O(n)으로 줄이면서 캐싱 메커니즘을 내부적으로 처리한다.
- 이는 성능을 개선할 뿐만 아니라 memoization 과정을 추상화하여 코드를 단순화한다.

<br>

# Runtime Comparison

위에서 구현한 4가지 방식의 함수로 실행 시간을 측정하고, 비교해보자!

```python
import timeit


functions = [
    fibonacci_recursive,
    fibonacci_top_down,
    fibonacci_bottom_up_list,
    fibonacci_bottom_up_dict,
    fibonacci_cache
]

# 각 함수의 실행 시간 측정
for func in functions:
    timer = timeit.Timer(lambda: func(30))
    elapsed_time = timer.timeit(number=100)  # 100번 실행 평균 시간
    print(f"{func.__name__}: {elapsed_time:.8f} seconds")
```

```
fibonacci_recursive: 16.82809308 seconds
fibonacci_top_down: 0.00059779 seconds
fibonacci_bottom_up_list: 0.00021792 seconds
fibonacci_bottom_up_dict: 0.00024538 seconds
fibonacci_cache: 0.00000704 seconds
```

<br>

# Conclusion

📊 4가지 구현 방식 성능 비교!

## 1) Time Complexity & Runtime

> Recursive > Top-Down > Bottom-Up DP(dictionary) > Bottom-Up DP(list) > Cache

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
