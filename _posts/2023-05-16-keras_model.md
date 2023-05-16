---
title: "[DL] 간단한 모델 만들어보며 `Keras` 이해하기"
excerpt: ""
categories:
  - DL
tags:
  - Keras
last_modified_at: 2023-05-16
---

`Keras`를 이용해 간단한 모델을 만들어보자.
- 목표: Keras 모델에 대한 이해!

<br>

# 모델 만들기

문제정의
- X 데이터: 강아지와 고양이 마리 수
  y 데이터: 먹이 갯수
- 예측: 강아지와 고양이가 각각 20마리씩 있을 때 필요한 먹이 갯수는?

<br>

```python
import tensorflow as tf
import numpy as np


X = np.asarray([
    [5, 1], [7, 3], [11, 9], [19, 21], [3, 15], [2, 13], [7, 2], [6, 7]
])
y = np.asarray([13, 23, 49, 101, 51, 43, 20, 33])

x_input = tf.keras.layers.Input(2)
x_hidden = tf.keras.layers.Dense(4)(x_input)
x_output = tf.keras.layers.Dense(1)(x_hidden)

models = tf.keras.Model(inputs=x_input, outputs=x_output)
models.compile(loss=tf.keras.losses.mean_squared_error)
models.fit(X, y, epochs=2000)

print(models.predict([[20, 20]]))
```

```
--------------------------------------------------
[[100.129715]]
```

<br>

# `Keras` 라이브러리 4가지 주요 구성요소

+ Layer: 딥러닝 모델 `내부`설계 때 이용되는 다양한 코드가 정의되어 있다
+ Model: 딥러닝 모델 `외부`설계 때 이용되는 다양한 코드가 정의되어 있다
+ Loss: 딥러닝 모델의 학습방향을 정하는 다양한 코드가 정의되어 있다
+ Optimizer: 딥러닝 모델 학습스타일을 정하는 다양한 코드가 정의되어 있다

<br>

## 1) Layer

+ 딥러닝 모델 `내부`설계 관련 다양한 코드들이 정의되어 있는 코드뭉치

### (1) Input
+ 입력 부분
### (2) Dense
+ 곱셈 연산 코드
### (3) Conv2D
+ 딥러닝 연산 코드 (Convolution)

<br>

## 2) Model

+ 딥러닝 모델 `외부`설계 관련 코드들이 정의되어 있는 코드뭉치

### (1) compile
+ 딥러닝 모델 내부 설계 시 Layer 하위의 다양한 코드들로 내부설계 완료 후, 각 Layer들을 자동으로 연결하는 코드
### (2) fit
+ 딥러닝 모델 학습 코드
### (3) predict
+ 학습 완료된 딥러닝 모델 예측 코드

<br>

## 3) Loss
+ 모델이 예측한 결과값과 실제 정답과이 차이를 계산하는 다양한 스타일의 손실함수(Loss Function)이 정의되어 있는 코드뭉치

<br>

## 4) Optimizer
+ Loss를 통해서 예측값과 실제 정답과의 차이를 최소화하는 방향을 찾은 상태에서, 어떤 스타일로 모델을 학습할지 다양한 스타일을 정의해둔 코드뭉치
+ 딥러닝 모델은 최적의 상태(=Optimal 값)를 지향하도록 학습된다.
+ `최적`: 학습에 사용되지 않은 데이터에 대해 올바르게 예측하는 값
+ 즉, 딥러닝 학습과정은 많은 데이터에 대해서 일반화(Generalization)를 하는 작업이고, 최대한 많은 데이터에 대해 올바르게 예측하도록 학습되어야 좋은 모델이라고 할 수 있다.
+ 이러한 최적화를 시키는 역할이 Optimizer