---
title: "[DL] Tensorboard & Early Stopping"
excerpt: "의류 이미지 구분 DL 프로젝트 - 실험 자동화"
categories:
 - DeepLearning
tags:
  - tensorflow
  - tensorboard
  - earlystopping
  - coding apple
last_modified_at: 2022-10-04
---

> Tensorboard를 통해 모델의 학습 진행상황을 시각적으로 확인할 수 있다. <br>
> 그리고 EarlyStopping이라는 함수를 통해 epoch이 진행되면서 accuracy와 loss의 진전이 없으면 epoch을 알아서 중단시키도록 설정할 수 있다.

| 의류 이미지 구분 DL 프로젝트

본 포스팅은 본 블로그에서 기존에 진행했던 
[[DL] Image Classification DL 모델 만들기 1](https://aijinsol.github.io/deeplearning/fashion_mnist/) 프로젝트의 코드에 Tensorboard와 EarlyStopping 코드를 추가하는 방식으로 작성하였다.

# 1. Tensorboard 시각화

## 1) Tensorboard 로그파일 생성

Tensorboard를 활용하기 위해서는 아래 코드를 추가해준다.
```python
from tensorflow.keras.callbacks import TensorBoard

tensorboard = Tensorboard( log_dir='logs/{}'.format( 'my_model' + str(int(time.time())) ) )

model.fit(trainX, trainY, validation_data=(testX, testY), epochs=3, callbacks=[tensorboard])
```

<br>

Example)
```python
import tensorflow as tf 
from tensorflow.keras.callbacks import TensorBoard


(trainX, trainY), (testX, testY) = tf.keras.datasets.fashion_mnist.load_data()

trainX = trainX / 255.0
testX = testX / 255.0

trainX = trainX.reshape( (trainX.shape[0], 28,28,1) )
testX = testX.reshape( (testX.shape[0], 28,28,1) )

model = tf.keras.Sequential([
	tf.keras.layers.Conv2D( 32, (3,3), padding='same', activation='relu', input_shape=(28,28,1) ),
	tf.keras.layers.MaxPooling2D( (2,2) ),
    tf.keras.layers.Flatten(),
    tf.keras.layers.Dense(128, activation='relu'),
    tf.keras.layers.Dense(10, activation='softmax'),
])

model.compile(loss='sparse_categorical_crossentropy', optimizer='adam', metrics=['acc'])

tensorboard = Tensorboard( log_dir='logs/{}'.format( 'my_model' + str(int(time.time())) ) )  # 현재 시간 기준으로 로그파일 생성

model.fit(trainX, trainY, validation_data=(testX, testY), epochs=3, callbacks=[tensorboard])
```

+ `Tensorboard()`: Tensorboard 시각화를 위한 로그파일 생성해주는 함수
	+ `log_dir`: 로그파일 저장경로
+ `callbacks=[tensorboard]`: epoch 끝날 때마다 acc, loss 기록된 로그파일 생성해준다. 
+ 현재 시각을 기준으로 로그 기록 남기기

	```python
	import time

	time.time()  # 초 단위로 현재시간 출력
	```

<br>

## 2) log파일 Tensorboard에 띄우기

### (1) Google Colab
```python
%load_ext tensorboard
%tensorboard --logdir logs
```

### (2) 로컬
```bash
$ tensorboard --logdir logs
```

<br>

## 3) 다양한 실험하기

Tensorboard로 다양한 실험을 진행한 결과를 확인하고 싶다면 <br>
	⇒ 레이어 만들기 & `model.fit()`을 여러번 반복 (for반복문 또는 def함수 활용)

Example) 아래처럼 모델을 함수화해서 모델을 여러개 생성하고, 각 모델에 대한 학습을 진행해도 좋다.

```python
def my_model(parameter):
	model = tf.keras.Sequential([
		tf.keras.layers.Conv2D( 32, (3,3), padding='same', activation='relu', input_shape=(28,28,1) ),
		tf.keras.layers.MaxPooling2D( (2,2) ),
		tf.keras.layers.Flatten(),
		tf.keras.layers.Dense(parameter, activation='relu'),
		tf.keras.layers.Dense(10, activation='softmax'),
	])
	return model

new_model = my_model(256)
```

<br>

# 2. EarlyStopping

모델이 accuracy 또는 loss 측면에서 더 이상 성능의 개선이 없을 때 알아서 epoch을 중단하도록 할 수 있다.

```python
from tensorflow.keras.callbacks import EaryStopping

es = EarlyStopping(monitor ='val_loss', patience=3, mode='min')

model.fit(trainX, trainY, validation_data=(testX, testY), epochs=300, callbaccks=[tensorboard, es])
```

+ 처음에 epoch 설정시 큰 값으로 넣어놓는다.
+ `es = EarlyStopping(monitor ='val_loss', patience=3, mode='min')`, `es = EarlyStopping(monitor ='val_accuracy', patience=3, mode='max')`
	+ epoch 끝날 때마다 val_loss 또는 val_accuracy를 모니터링하겠다. epoch 3개 지나도 진전이 없으면 epoch을 알아서 중지해준다.

<br>

# Reference
+ [코딩애플 - 문과여도 상관없다 Tensorflow 딥러닝 AI 기초부터 실무까지](https://codingapple.com/course/python-deep-learning/)