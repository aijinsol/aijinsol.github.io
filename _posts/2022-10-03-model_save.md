---
title: "[DL] model/checkpoint 저장하기"
excerpt: "의류 이미지 구분 DL 프로젝트"
categories:
 - DeepLearning
tags:
  - tensorflow
  - codding apple
last_modified_at: 2022-10-03
---

> 학습이 끝나나 모델을 파일로 저장하면, 모델을 파일로 이동할 수도 있고 학습을 잠깐 중지하고 다른 날 다시 시작할 수도 있다. 또, 실험 후에 best 결과만 따로 저장할 수도 있다. <br>
> 특히, AI 기반 웹/앱 서비스를 만들 때 누군가 사진을 업로드하면 사진을 파이썬으로 해체해서 숫자로 만든 뒤에 `model.predict(사진데이터)`를 통해 출력결과를 유저에게 알려줄 수 있다. 이때 필요한 것이 학습이 완료된 모델인데 이 모델을 기존에 파일로 저장해놓고 쉽게 불러와 활용할 수 있다.

| 의류 이미지 구분 DL 프로젝트

# 1. 모델 저장하는 2가지 방법
Model을 저장하는 방법에는 2가지가 있다.

## 1) Model 전체 저장 / 로드
+ 저장되는 요소들
    + Model layers
    + loss 함수
    + optimizer
    + 가중치(w값들)

```python
# Save model
model.save('folder/model1')

# Load model
my_model = tf.keras.models.load_model('foler/model1')
my_model.summary()

# Evaluate
my_model.evaluate(testX, testY)    # 새로운 데이터로 모델 평가
```

<br>

## 2) Checkpoint 저장 (w값 저장)
+ 저장되는 요소들
    + 가중치(w값들)

Checkpoint로 저장할 때는 가중치(w값들)만 저장하며, epoch 중간중간에 저장할 수 있다는 장점이 있다. <br>
Checkpoint로 저장하기 위해서는 콜백함수를 만들고, 학습시에 콜백함수를 파라미터로 넣어준다.

```python
callback_func = tf.keras.callbacks.ModelCheckpoint(
	filepath='checkpoint/mnist{epoch}',
	monitor = 'val_acc',
	mode='max',
	save_weights_only=True,
	save_freq='epoch'    # epoch 끝날 때마다 checkpoint 저장해줌
)

model.fit(trainX, trainY, validation_data=(testX, testY), epochs=3, callbacks=[callback_func])    # epoch 하나 끝날 때마다 callback 함수 실행
```

+ 콜백함수
    + epoch 단위로 checkpoint 저장 가능
    + epoch마다 checkpoint 저장할 때 덮어쓰지 않고, 매 epoch마다 새로 생성되는 checkpoint를 따로 저장하기 위해서는 ⇒ `filepath='checkpoint/mnist{epoch}'`
    + 위에서 `{epoch}` 없애면 마지막 epoch의 w값만 저장된다 (기존 파일 덮어쓰기)
    + Monitoring을 하면서 val_acc가 최대가 되는 checkpoint만 저장 <br>
        ⇒ `monitor = 'val_acc'`, `mode='max'`
    + 콜백함수를 만들어줬다면, `model.fit`할 때 파라미터로 `callbacks=[callback_func]`을 추가해준다.

Example)
```python
import tensorflow as tf


(trainX, trainY), (testX, testY) = tf.keras.datasets.fashion_mnist.load_data()

trainX = trainX / 255.0
testX = testX / 255.0

trainX = trainX.reshape( (trainX.shape[0], 28,28,1) )
testX = testX.reshape( (testX.shape[0], 28,28,1) )

model = tf.keras.Sequential([
    tf.keras.layers.Flatten( input_shape=(28,28,1) ),
    tf.keras.layers.Dense(128, activation='relu'),
    tf.keras.layers.Dense(10, activation='softmax'),
])

callback_func = tf.keras.callbacks.ModelCheckpoint(
	filepath='checkpoint/mnit{epoch}',
	save_weights_only=True,
	save_freq='epoch'
)

model.compile(loss='sparse_categorical_crossentropy', optimizer='adam', metrics=['acc'])
model.fit(trainX, trainY, validation_data=(testX, testY), epochs=3, callbacks=[callback_func])
```

Checkpoint로 저장해놨으면 (w값만 저장되어있다면) 모델을 만들고 checkpoint 파일(w값)을 로드하면 된다.

```python
# Build model
model2 = tf.keras.Sequential([
    tf.keras.layers.Flatten( input_shape=(28,28,1) ),
    tf.keras.layers.Dense(128, activation='relu'),
    tf.keras.layers.Dense(10, activation='softmax'),
])

model2.summary()

model2.compile(loss='sparse_categorical_crossentropy', optimizer='adam', metrics=['acc'])

# Load model
model2.load_weights('checkpoint/mnist5')    # 원하는 epoch의 checkpoint 파일 경로 입력

# Evaluate
model2.evaluate(testX, testY)
```

# Reference
+ [코딩애플 - 문과여도 상관없다 Tensorflow 딥러닝 AI 기초부터 실무까지](https://codingapple.com/course/python-deep-learning/)