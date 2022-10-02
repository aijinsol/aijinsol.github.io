---
title: "[DL] Image Classification DL 모델 만들기 - 개 vs 고양이 구분"
excerpt: "(Kaggle) 개 고양이 구분 DL 프로젝트"
categories:
 - DeepLearning
tags:
  - keras
  - codding apple
last_modified_at: 2022-09-29
---

> Kaggle 프로젝트의 실전 데이터로 프로젝트 진행해보기 <br>
> [Kaggle: Dogs vs. Cats Redux: Kernels Edition](https://www.kaggle.com/c/dogs-vs-cats-redux-kernels-edition)

| 개 고양이 구분 DL 프로젝트

```python
import os
import shutil
import tensorflow as tf


# Data Download
os.environ['KAGGLE_CONFIG_DIR'] = '/Users/jinsolkim/.kaggle/'

!kaggle competitions download -c dogs-vs-cats-redux-kernels-edition

!unzip -q dogs-vs-cats-redux-kernels-edition.zip -d .
!unzip -q train.zip -d .

os.mkdir('./dataset')
os.mkdir('./dataset/cat')
os.mkdir('./dataset/dog')

for i in os.listdir('./train/'):
    if 'cat' in i:
        shutil.copyfile('./train/' + i, './dataset/cat/' + i)
    if 'dog' in i:
        shutil.copyfile('./train/' + i, './dataset/dog/' + i)

# Pre-processing
train_ds = tf.keras.preprocessing.image_dataset_from_directory(
    './dataset/',
    image_size=(64,64),
    batch_size=64,
    subset='training',
    validation_split=0.2,
    seed=100
)

val_ds = tf.keras.preprocessing.image_dataset_from_directory(
    './dataset/',
    image_size=(64,64),
    batch_size=64,
    subset='validation',
    validation_split=0.2,
    seed=100
)

def pre_process(i, ans):
    i = tf.cast(i/255.0, tf.float32)
    return i, ans

train_ds = train_ds.map(pre_process)
val_ds = val_ds.map(pre_process)

# Model
model = tf.keras.Sequential([
    tf.keras.layers.Conv2D(32, (3, 3), padding='same', activation='relu', input_shape=(64, 64, 3)),
    tf.keras.layers.MaxPooling2D((2, 2)),
    tf.keras.layers.Conv2D(64, (3, 3), padding='same', activation='relu'),
    tf.keras.layers.MaxPooling2D((2, 2)),
    tf.keras.layers.Dropout(0.2),
    tf.keras.layers.Conv2D(128, (3, 3), padding='same', activation='relu'),
    tf.keras.layers.MaxPooling2D((2, 2)),
    tf.keras.layers.Flatten(),
    tf.keras.layers.Dense(128, activation='relu'),
    tf.keras.layers.Dropout(0.2),
    tf.keras.layers.Dense(1, activation='sigmoid')
])

model.summary()

model.compile(loss='binary_crossentropy', optimizer='adam', metrics=['accuracy'])
model.fit(train_ds, validation_data=val_ds, epochs=5)
```

<br>

# 1. Kaggle Data Download

+ Kaggle 홈페이지에서 `My Account > API - Create New API Token` 클릭
+ 다운로드 된 kaggle.json 파일을 `~/.kaggle/`에 위치시기키 (Mac 기준)

```python
import os

os.environ['KAGGLE_CONFIG_DIR'] = '/Users/jinsolkim/.kaggle/'

!kaggle competitions download -c dogs-vs-cats-redux-kernels-edition
```

다운로드 된 zip파일 압축 풀기
```python
!unzip -q dogs-vs-cats-redux-kernels-edition.zip -d .
!unzip -q train.zip -d .
```

이미지 파일 갯수 확인
```python
print(len(os.listdir('./train/')))
```

<br>

# 2. Data Preprocessing

<div class="notice--info" markdown="1">
💡 Image classification할 때는 `dataset` 폴더 안에 각 카테고리 별로 폴더 만들어서 해당하는 데이터셋 옮겨놓기!! (항상!⭐️)
</div>

## 1) Dataset

dataset 폴더 만들기

```python
os.mkdir('./dataset')
os.mkdir('./dataset/cat')
os.mkdir('./dataset/dog')
```

개 vs 고양이 파일 파일 분류해서 dataset 폴더로 복사
```python
import shutil

for i in os.listdir('./train/'): # 파일명 (ex. cat.5077.jpg)
    if 'cat' in i:
        shutil.copyfile('./train/' + i, './dataset/cat/' + i)
    if 'dog' in i:
        shutil.copyfile('./train/' + i, './dataset/dog/' + i) 
```

<br>

## 2) Image to Tensor

이미지 숫자로 변환하기

```python
train_ds = tf.keras.preprocessing.image_dataset_from_directory(
    './dataset/',
    image_size=(64,64),
    batch_size=64,
    subset='training',
    validation_split=0.2,
    seed=100
)

val_ds = tf.keras.preprocessing.image_dataset_from_directory(
    './dataset/',
    image_size=(64,64),
    batch_size=64,
    subset='validation',
    validation_split=0.2,
    seed=100
)

print(train_ds)
print(val_ds)


------------------------------------------------------------
Found 25000 files belonging to 2 classes.
Using 20000 files for training.
Found 25000 files belonging to 2 classes.
Using 5000 files for validation.
<BatchDataset element_spec=(TensorSpec(shape=(None, 64, 64, 3), dtype=tf.float32, name=None), TensorSpec(shape=(None,), dtype=tf.int32, name=None))>
<BatchDataset element_spec=(TensorSpec(shape=(None, 64, 64, 3), dtype=tf.float32, name=None), TensorSpec(shape=(None,), dtype=tf.int32, name=None))>
```

+ `tf.keras.preprocessing.image_dataset_from_directory()` : 폴더 내 이미지들을 바로 숫자화된 dataset으로 만들어 준다.
+ `image_size=(64,64)` : 모든 이미지를 64x64 pixel로 압축
+ `batch_size=64` : 이미지 2만장을 모델에 한번에 넣지 않고 batch 숫자만큼 넣도록
+ `subset='training'` : train dataset 이름 붙이기. 아래 `validation_split`에 따라 이 데이터는 전체 데이터의 80%)
    `subset='validation'` : validation dataset 이름 붙이기. 아래 `validation_split`에 따라 이 데이터는 전체 데이터의 80%)
+ `validation_split=0.2`
    + dataset에서 validation dataset split
    + `train_ds` 블록 밑에 `val_ds`도 똑같은 형태로 한번 더 써줘야 한다.
    + `train_ds` 블록에서는 `subset='training'`, `val_ds `블록에서는 `subset='validation'` 써줘서 각각 무슨 데이터를 의미하는지 나타내주기!
+ `train_ds`는 `( (xxx), (yyy) )` 형태. 즉, `( (이미지 2만개), (정답 2만개) )`.

<br>

## 3) Image Pre-processing

데이터 0~1 사이의 값으로 scaling
```python
def pre_process(i, ans):
    i = tf.cast(i/255.0, tf.float32)
    return i, ans

train_ds = train_ds.map(pre_process)
val_ds = val_ds.map(pre_process)
```

데이터셋 확인
```python
import matplotlib.pyplot as plt

for i, ans in train_ds.take(1):
    print(i.shape)
    print(i)
    print(ans.shape)
    print(ans)
    plt.imshow(i[0].numpy().astype('uint8'))
    plt.show()


------------------------------------------------------------
(64, 64, 64, 3)
tf.Tensor(
[[[[0.23100491 0.21568628 0.20318627]
   [0.1858226  0.1740579  0.13876379]
   [0.18834636 0.17266008 0.16089538]
   ...
   [0.16947381 0.1069738  0.07952283]
   [0.13084023 0.11123239 0.08770297]
   [0.13333334 0.11372549 0.09019608]]

  [[0.23925781 0.22393918 0.21143919]
   [0.1872664  0.17550169 0.14020757]
   [0.18532859 0.16964231 0.15787761]
   ...
   [0.16032858 0.10784314 0.07745098]
   [0.1420343  0.12242647 0.09889706]
   [0.13725491 0.11764706 0.09411765]]

  [[0.25       0.22683823 0.23284313]
   [0.21454887 0.184892   0.16246553]
   [0.23345588 0.18639706 0.1942402 ]
   ...
   [0.1764706  0.14901961 0.11764706]
   [0.17058824 0.12745099 0.11960784]
   [0.17634805 0.13321078 0.12536764]]
...
(64,)
tf.Tensor(
[0 1 1 0 0 1 0 1 1 0 0 1 1 0 1 1 0 1 0 0 0 1 0 1 1 0 0 1 0 0 1 0 0 0 1 1 1
 1 0 0 1 1 1 0 0 1 0 0 0 1 0 0 1 1 0 1 0 1 0 0 1 1 1 0], shape=(64,), dtype=int32)
```

+ `i = tf.cast( i/255.0, tf.float32 )`
    + (바로 `i = i/255.0` 로 하면 좋겠지만) tensor이므로 `tf.cast` 사용
    + `tf.float32` : 만약을 위해서 자료형 강제

<br>

# 3. Model

```python
model = tf.keras.Sequential([
    tf.keras.layers.Conv2D(32, (3, 3), padding='same', activation='relu', input_shape=(64, 64, 3)),
    tf.keras.layers.MaxPooling2D((2, 2)),
    tf.keras.layers.Conv2D(64, (3, 3), padding='same', activation='relu'),
    tf.keras.layers.MaxPooling2D((2, 2)),
    tf.keras.layers.Dropout(0.2),
    tf.keras.layers.Conv2D(128, (3, 3), padding='same', activation='relu'),
    tf.keras.layers.MaxPooling2D((2, 2)),
    tf.keras.layers.Flatten(),
    tf.keras.layers.Dense(128, activation='relu'),
    tf.keras.layers.Dropout(0.2),    # 128개의 노드 중 20% 제거
    tf.keras.layers.Dense(1, activation='sigmoid')
])

model.summary()


------------------------------------------------------------
Model: "sequential_2"
_________________________________________________________________
 Layer (type)                Output Shape              Param #   
=================================================================
 conv2d_4 (Conv2D)           (None, 64, 64, 32)        896       
                                                                 
 max_pooling2d_4 (MaxPooling  (None, 32, 32, 32)       0         
 2D)                                                             
                                                                 
 conv2d_5 (Conv2D)           (None, 32, 32, 64)        18496     
                                                                 
 max_pooling2d_5 (MaxPooling  (None, 16, 16, 64)       0         
 2D)                                                             
                                                                 
 dropout (Dropout)           (None, 16, 16, 64)        0         
                                                                 
 conv2d_6 (Conv2D)           (None, 16, 16, 128)       73856     
                                                                 
 max_pooling2d_6 (MaxPooling  (None, 8, 8, 128)        0         
 2D)                                                             
                                                                 
 flatten_3 (Flatten)         (None, 8192)              0         
                                                                 
 dense_5 (Dense)             (None, 128)               1048704   
                                                                 
 dropout_1 (Dropout)         (None, 128)               0         
                                                                 
 dense_6 (Dense)             (None, 1)                 129       
                                                                 
=================================================================
Total params: 1,142,081
Trainable params: 1,142,081
Non-trainable params: 0
_________________________________________________________________
```
```python
model.compile(loss='binary_crossentropy', optimizer='adam', metrics=['accuracy'])
model.fit(train_ds, validation_data=val_ds, epochs=5)
```

+ Dropout 레이어
    + overfitting 완화 가능
    + 윗 레이어의 노드를 일부 제거해준다.
    + ex) `tf.keras.layers.Dropout(0.2)` : 윗 레이어의 노드 중 20% 제거해주세요.

<br>

# 4. Results

+ 데이터 전처리 0~1 scaling 했을 때 vs 안 했을 때 모델 성능 차이
    + 데이터 전처리하지 않았을 때
    ```bash
    Epoch 1/5
    313/313 [==============================] - 10s 26ms/step - loss: 2.5032 - accuracy: 0.5839 - val_loss: 0.6012 - val_accuracy: 0.6806
    Epoch 2/5
    313/313 [==============================] - 8s 25ms/step - loss: 0.5912 - accuracy: 0.6789 - val_loss: 0.6017 - val_accuracy: 0.6486
    Epoch 3/5
    313/313 [==============================] - 8s 25ms/step - loss: 0.5473 - accuracy: 0.7165 - val_loss: 0.5435 - val_accuracy: 0.7136
    Epoch 4/5
    313/313 [==============================] - 8s 25ms/step - loss: 0.5230 - accuracy: 0.7341 - val_loss: 0.5169 - val_accuracy: 0.7546
    Epoch 5/5
    313/313 [==============================] - 8s 25ms/step - loss: 0.4988 - accuracy: 0.7554 - val_loss: 0.5080 - val_accuracy: 0.7536
    ```
    + 데이터 전처리했을 때 (0~1 scaling)
    ```bash
    Epoch 1/5
    313/313 [==============================] - 8s 23ms/step - loss: 0.5454 - accuracy: 0.7229 - val_loss: 0.5101 - val_accuracy: 0.7456
    Epoch 2/5
    313/313 [==============================] - 8s 24ms/step - loss: 0.4639 - accuracy: 0.7816 - val_loss: 0.4541 - val_accuracy: 0.7910
    Epoch 3/5
    313/313 [==============================] - 7s 23ms/step - loss: 0.4262 - accuracy: 0.8047 - val_loss: 0.4284 - val_accuracy: 0.7974
    Epoch 4/5
    313/313 [==============================] - 7s 23ms/step - loss: 0.3850 - accuracy: 0.8260 - val_loss: 0.3896 - val_accuracy: 0.8176
    Epoch 5/5
    313/313 [==============================] - 7s 23ms/step - loss: 0.3539 - accuracy: 0.8416 - val_loss: 0.3769 - val_accuracy: 0.8208
    ```
+ Input data의 숫자를 0~1 사이로 압축하면 ⇒ 연산 속도 빨라지고(최적의 w값 빨리 찾는다), accuracy 증가.
+ val_acc 약 85%가 이 모델에서의 최대 accuracy.
    + 문제는 데이터의 퀄리티.
    + 결국, 데이터의 전처리(양, 질)가 모델의 성능을 높이는 데에 큰 효과를 가져온다!


# Reference
+ [코딩애플 - 문과여도 상관없다 Tensorflow 딥러닝 AI 기초부터 실무까지](https://codingapple.com/course/python-deep-learning/)