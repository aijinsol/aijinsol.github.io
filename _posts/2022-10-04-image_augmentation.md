---
title: "[DL] Image Augmentation"
excerpt: "(Kaggle) 개 vs 고양이 구분 DL 프로젝트"
categories:
 - DeepLearning
tags:
  - tensorflow
  - coding apple
last_modified_at: 2022-10-04
---

> 모델의 성능을 높이기 위해서는 데이터의 양을 늘리는 방법이 좋다. 데이터의 양을 늘리는 방법에는 새로운 데이터를 구하는 방법 외에도 Image Augmentation라는 방법이 있다. 기존의 이미지에 살짝씩 변형을 주어 모델이 서로 다른 이미지라고 인식하도록 만드는 것이다. 이렇게 하면 overfitting도 완화되고, accuracy도 높아진다.

| 개 고양이 구분 DL 프로젝트

본 포스팅은 본 블로그에서 기존에 진행했던 
[[DL] Image Classification DL 모델 만들기 2](https://aijinsol.github.io/deeplearning/dogs_vs_cats/) 프로젝트의 코드에 이미지 증강 코드를 추가하는 방식으로 작성하였다.

# (방법 1) 모델에 넣기 직전 이미지 증강

```python
tf.keras.layers.experimental.preprocessing.${CLASSES}
```
+ 최근에 새로 나온 방식으로 보통 이 모듈을 활용한 방식으로 augmentation을 많이 진행한다.
+ `${CLASSES}`에 들어갈 다양한 전처리 classes는 [Tensorflow 소개 페이지(Tensorflow - Module:tf.keras.layers.experimental.preprocessing)](https://www.tensorflow.org/api_docs/python/tf/keras/layers/experimental/preprocessing)에서 확인할 수 있다.

Example)
```python
model = tf.keras.Sequential([

    tf.keras.layers.experimental.preprocessing.RandomFlip('horizontal', input_shape=(64, 64, 3)), # 사진 뒤집기
    tf.keras.layers.experimental.preprocessing.RandomRotation(0.1),
    tf.keras.layers.experimental.preprocessing.RandomZoom(0.1),

    tf.keras.layers.Conv2D(32, (3, 3), padding='same', activation='relu'),
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
+ 위 코드 돌리면
    + 증강 안했을 때는 training accuracy 95% 이상, validation accuracy는 83~85% 정도
    + 증강하면 training, validation accuracy 모두 86% 정도로 비슷하게 나온다.
    + 즉, overfitting 현상이 해결되는 현상을 보였다.
+ 극적인 효과를 위해 이미지를 64x64가 아닌 조금 더 큰 이미지(128x128 정도) 쓰면 학습효과도 더 나아질 것이다. 물론 시간은 더 오래 걸리겠지만!

<br>

# (방법 2) Tensorflow 전통방식의 이미지 증강

+ 위에서 소개한 최근에 새로 나온 방식이 아닌 Tensorflow 전통방식으로 이미지를 증강하는 방법은 <br>
    ⇒ 애초에 이미지 데이터셋을 만들 때 이미지 증강을 적용하는 방식이다.

```python
from tensorflow.keras.preprocessing.image import ImageDataGenerator


generator = ImageDataGenerator(   # 이미지 전처리해주는 함수
		rescale = 1./255,
		rotation_range=20,        # 회전
		zoom_range=0.15,          # 확대
		width_shift_range=0.2,    # 이동
		height_shift_range=0.2,
		shear_range=0.15,         # 굴절
		horizontal_flip=True,     # 가로반전
		fill_mode='nearest')      # 이미지 이동시키면 생기는 공백을 근처에 있는 픽셀로 채우기

train_ds = generator.flow_from_directory(
		'/content/train_dataset', # 학습용 데이터셋 경로
		class_mode='binary',
		shuffle=True,
		seed=123,
		color_mode='rgb',
		batch_size=64,
		target_size=(64, 64)
)

generator2 = ImageDataGenerator(rescale=1./255)

val_ds = generator2.flow_from_directory(
		'/content/val_dataset',   # 검증용 데이터셋 경로
		class_mode='binary',
		shuffle=True,
		seed=123,
		color_mode='rgb',
		batch_size=64
)

# model.fit(train_ds, epochs=1)
```

+ Step: `원본이미지` → `원본이미지 + 증강된 이미지 n개` → `모델 학습`
+ `flow_from_directory`: `image_dataset_from_directory()`와 같은 역할
+ `class_mode`: 분류할 이미지가 2개면 `binary`, 3개 이상이면 `categorical`
+ 흔히 하는 실수 중 하난가 training dataset만 전처리하고, validation dataset은 전처리 안하는 것! 반드시 둘 다 해주자!
+ `generator2 = ImageDataGenerator(rescale=1./255)`: validation dataset은 이미지 뒤틀 필요 없다! 255로 나누기만 해주자.
+ 원래 데이터셋을 training용, validation용으로 구분해서 디렉토리 구성해서 작업!

<br>

# Reference
+ [코딩애플 - 문과여도 상관없다 Tensorflow 딥러닝 AI 기초부터 실무까지](https://codingapple.com/course/python-deep-learning/)