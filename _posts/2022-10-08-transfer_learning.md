---
title: "[DL/CV/PRJ] Transfer Learning (전이학습)"
excerpt: "(Kaggle) 개 고양이 구분 DL 프로젝트 #3 - "
categories:
  - DeepLearning
  - ComputerVision
  - Project
tags:
  - tensorflow
  - transfer learning
  - coding apple
last_modified_at: 2022-10-08
---

> 전이학습을 이용해서 내 모델의 성능을 올려보자.

| 개 고양이 구분 DL 프로젝트

본 포스팅은 구글의 InceptionV3 모델에 본 블로그에서 기존에 진행했던 
[[DL] Image Classification DL 모델 만들기 2](https://aijinsol.github.io/deeplearning/dogs_vs_cats/) 프로젝트의 코드를 전이학습으로 연결하여 모델을 만드는 과정을 담고 있다.

# Code

## 1) Data Download & Preprocess

```python
import os
import tensorflow as tf 
import shutil


# Data Download
os.environ['KAGGLE_CONFIG_DIR'] = '/content/'
!kaggle competitions download -c dogs-vs-cats-redux-kernels-edition
!unzip -q train.zip -d .

# Data Pre-process
os.mkdir('/content/dataset')
os.mkdir('/content/dataset/cat')
os.mkdir('/content/dataset/dog')

for i in os.listdir('/content/train/'):
  if 'cat' in i:
    shutil.copyfile( '/content/train/' + i , '/content/dataset/cat/' + i )
  if 'dog' in i:
    shutil.copyfile( '/content/train/' + i , '/content/dataset/dog/' + i )

train_ds = tf.keras.preprocessing.image_dataset_from_directory(
  '/content/dataset/',
  image_size=(150,150),  # 이미지 전처리시 input_shape과 동일하게 150x150으로 설정
  batch_size=64,
  subset='training',
  validation_split=0.2,
  seed=1234
)

val_ds = tf.keras.preprocessing.image_dataset_from_directory(
  '/content/dataset/',
  image_size=(150,150),
  batch_size=64,
  subset='validation',
  validation_split=0.2,
  seed=1234
)

print(train_ds)

def pre_process(i, ans):
  i = tf.cast( i/255.0, tf.float32 )
  return i, ans

train_ds = train_ds.map(pre_process)
val_ds = val_ds.map(pre_process)
```

## 2) Weight & Model Download
```python
import requests
from tensorflow.keras.applications.inception_v3 import InceptionV3


# Weight Download
url = 'https://storage.googleapis.com/mledu-datasets/inception_v3_weights_tf_dim_ordering_tf_kernels_notop.h5'
r = requests.get(url, allow_redirects=True)

open('inception_v3.h5', 'wb').write(r.content)


# Model Download
inception_model = InceptionV3(input_shape=(150,150,3), include_top=False, weights=None)
```

## 3) Transfer Learning
```python
import tensorflow as tf


# Load weights
inception_model.load_weights('inception_v3.h5')

# Setting layers to freeze or unfreeze
for i in inception_model.layers:
	i.trainable = False

unfreeze = False
for i in inception_model.layers:
	if i.name == 'mixed6':
		unfreeze = True
	if unfreeze == True:
		i.trainable = True

top_layer = inception_model.get_layer('mixed7')

# InceptionV3 + MyModel
layer1 = tf.keras.layers.Flatten()(top_layer.output)
layer2 = tf.keras.layers.Dense(1024, activation='relu')(layer1)
drop1 = tf.keras.layers.Dropout(0.2)(layer2)
layer3 = tf.keras.layers.Dense(1, activation='sigmoid')(drop1)

model = tf.keras.Model(inception_model.input, layer3)

# Train
model.compile(loss='binary_crossentropy', optimizer=tf.keras.optimizers.Adam(lr=0.00001), metrics=['acc'])
model.fit(train_ds, validations_data=val_ds, epochs=2)
```

<br>

# 1. Transfer Learning

> + 전이학습은 이미 학습이 완료된 모델의 전부 또는 일부를 가져와 내 모델에 연결하여 학습시키는 것을 말한다.
> + 이미 성능이 좋기로 검증된 VGG, ResNet, AlexNet, GoogleNet/Inception 등의 모델을 가져와서 나의 모델에 연결하여 학습시키면 나의 새로운 모델의 성능이 크게 올라갈 수 있다.
> + 그런데 구글의 GoogleNet 모델은 1000개의 카테고리를 분류하는 모델이고, 만약 나의 모델은 개vs고양이 2개 카테고리만 분류하는 모델이라면?
>   ⇒ 출력층만 바꿔주면 된다!
> + 이처럼 전이학습 시에는 '모델의 전부' 또는 '일부'만 가져와서 학습시간을 절약하거나 모델의 성능을 향상시킬 수 있다.

<br>

지금부터 위의 코드를 한줄한줄 뜯어보자.

## 1) Weight & Model Download
```python
import requests
from tensorflow.keras.applications.inception_v3 import InceptionV3


url = 'https://storage.googleapis.com/mledu-datasets/inception_v3_weights_tf_dim_ordering_tf_kernels_notop.h5'
r = requests.get(url, allow_redirects=True)

open('inception_v3.h5', 'wb').write(r.content)

inception_model = InceptionV3( input_shape=(150,150,3), include_top=False, weights=None )
```
+ 전이학습을 위해서는 모델과 학습이 완료된 weight 파일이 필요하다. 둘을 같이 다운받을 수도 있지만 보통 모델과 학습 완료된 weight 파일을 각각 따로 다운 받는다.
  + 여기서 모델을 다운받는다는 의미는 해당 모델의 레이어 형태를 불러오는 것이다.
+ 다른 모델을 가져와서 사용할 때 `input_shape`과 `output_shape`를 주의해야한다!
  + 무조건 해당 모델의 레이어에 기록된 `input_shape, output_shape` 그대로 넣어야 모델 학습이 가능하다. 즉, 해당 모델을 만든 사람이 정한 `input_shape, output_shape`를 알아야 하는 것.
  + 만약 `input_shape`를 변경하고 싶다면 내가 직접 첫번째 레이어를 수정해주면 된다! `Tensorflow.keras`에서 `input_shape`를 내 마음대로 설정할 수 있게끔 파라미터를 제공한다.

<br>

+ `input_shape`: 원래 InceptionV3는 (299,299,3)이지만, 강제로 `input_shape=(150,150,3)`으로 설정해보았다.
+ `include_top=False`: top layer를 include해서 가져올지 말지 결정. `False`로 설정하여 가져오지 않도록 하였다.
  + `top layer`: 마지막 Output layer (Dense)
+ `weights=None`: weight를 load하겠습니까? 위에 weight 파일 미리 다운받아놨으니 따로 load할 필요 없으므로 `None`으로 설정.

<br>

## 2) Weight 파일 + InceptionV3 모델 합치기
```python
inception_model.load_weights('inception_v3.h5')

inception_model.summary()
```
+ 불러온 모델에 다운받은 weight 파일을 load하여 모델에 가중치파일을 입혀준다.

<br>

## 3) 학습금지레이어 설정
+ 모델과 가중치를 불러왔는데 이렇게 불러온 레이어를 새로 학습시키면 안된다. 이미 학습되어 있는 모델의 가중치를 활용하고자 하는 목적이니까. 그러면 레이어에 더 이상 학습이 일어나지 않도록 설정해보자.

```python
for i in inception_model.layers:
  i.trainable = False
```
+ `inception_model.layers` : layer 하나씩 출력해서 볼 수 있다.
+ `i.trainable = False` : w값 고정 (이 레이어는 학습시키지 않도록!)
+ (참고) 가끔 레이어를 학습하기도 한다. 아주 살짝 학습 일어나도록 하는데 이런걸 `Fine-tuning`이라고 한다.

Fine-tuning하는 법

```python
for i in inception_model.layers:
	i.trainable = False

unfreeze = False
for i in inception_model.layers:
	if i.name == 'mixed6':
		unfreeze = True
	if unfreeze == True:
		i.trainable = True # mixed6 레이어부터 train된다.
```
+ 위의 코드를 통해 InceptionV3 모델의 `mixed6` 레이어부터 학습이 일어나도록 설정하였다. (`unfreeze` 코드 활용!)

<br>

## 4) 원하는 레이어만 사용하기
+ 불러온 모델에서 원하는 레이어만 추출해서 사용할 수도 있다. ⇒ `Funcional API`를 쓰면 된다.

```python
last_layer = inception_model.get_layer('mixed7')

print(last_layer)
print(last_layer.output)
print(last_layer.output_shape)

------------------------------------------------------------
<keras.layers.merging.concatenate.Concatenate object at 0x2bfb7af80>
KerasTensor(type_spec=TensorSpec(shape=(None, 7, 7, 768), dtype=tf.float32, name=None), name='mixed7/concat:0', description="created by layer 'mixed7'")
(None, 16, 16, 256)
```
+ `inception_model.get_layer('mixed7')`: InceptionV3 모델의 `mixed7` 레이어를 불러왔다. 이 때 모델의 첫 레이어부터 `mixed7` 레이어까지 불러와진다.

<br>

## 5) 내 레이어에 연결하기
```python
import tensorflow as tf


layer1 = tf.keras.layers.Flatten()(last_layer.output)
layer2 = tf.keras.layers.Dense(1024, activation='relu')(layer1)
drop1 = tf.keras.layers.Dropout(0.2)(layer2)
layer3 = tf.keras.layers.Dense(1, activation='sigmoid')(drop1)

model = tf.keras.Model(inception_model.input, layer3) # 시작레이어, 마지막레이어 명시

model.summary()
```
+ `tf.keras.layers.Flatten()(last_layer.output)`
  + 위에서 마지막 레이어의 shape을 확인해보니 (None, 16, 16, 256)으로 매우 복잡해보였다. 마지막에 dense layer로 1개의 노드로 출력해주기 위해 Flatten()을 적용하였다.
  + `last_layer.output`: 괄호 안에 연결해주고 싶은 마지막 레이어의 output 값을 넣어준다.
+ `model = tf.keras.Model(inception_model.input, layer3)`: 모델의 시작과 끝을 명시해주는 부분
  + 반드시 input 모델에 `.input`을 적용해서 넣어준다.

<br>

## 6) 학습하기

```python
model.compile(loss='binary_crossentropy', optimizer=tf.keras.optimizers.Adam(lr=0.00001), metrics=['acc'])
model.fit(train_ds, validations_data=val_ds, epochs=2)
```
+ optimizer에 `adam` 대신 `tf.keras.optimizers.Adam(lr=0.00001)`을 입력하여 학습 시 weight들이 아주 조금씩 업데이트 되도록 설정하여 fine-tuning이 일어나도록 하였다.
+ 이 모델을 돌려보면 첫번째 epoch부터 accuracy 94%의 높은 성능을 보이는 것을 확인할 수 있다. (전이학습의 위력!)


<br>

# Reference
+ [코딩애플 - 문과여도 상관없다 Tensorflow 딥러닝 AI 기초부터 실무까지](https://codingapple.com/course/python-deep-learning/)