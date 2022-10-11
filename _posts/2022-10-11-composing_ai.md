---
title: "[DL] 작곡하는 AI 만들기"
excerpt: ""
categories:
 - DeepLearning
tags:
  - sequence
  - coding apple
last_modified_at: 2022-10-11
---

> 스스로 작곡하는 AI를 만들어보자.

# Code
```python
import numpy as np
import tensorflow as tf


text = open('pianoabc.txt', 'r').read()
unique_text = list(set(text))
unique_text = unique_text.sort()

# utilities
text_to_num = {}
num_to_text = {}

for i, data in enumerate(unique_text):
    text_to_num[data] = i
    num_to_text[i] = data

# original data -> numbers
numbered_text = []
for i in text:
    numbered_text.append(text_to_num[i])

X = []
Y = []
for i in range(0, len(numbered_text) - 25):
    X.append(numbered_text[i:i+25])
    Y.append(numbered_text[i+25])

# one-hot encoding
X = tf.one_hot(X, 31)
Y = tf.one_hot(Y, 31)

# model
model = tf.keras.models.Sequential([
    tf.keras.layers.LSTM(100, input_shape=(25, 31)),
    tf.keras.layers.Dense(31, activation='softmax')
])

model.compile(loss='categorical_crossentropy', optimizer='adam', metrics=['accuracy'])

model.fit(X, Y, batch_size=64, epochs=50, verbose=2)

model.save('model1')
```

```python
# load model
model = tf.keras.models.load_model('model1')

# make first input data for prediction
first_input = numbered_text[117:117+25]
first_input = tf.one_hot(first_input, 31)
first_input = tf.expand_dims(first_input, axis=0)

# prediction (compose!)
music = []
for i in range(200):
    pred_val = model.predict(first_input)
    pred_val = np.argmax(pred_val[0])

    music.append(pred_val)

    next_input = first_input[0][1:]
    one_hot_num = tf.one_hot(pred_val, 31)

    first_input = np.vstack([next_input, one_hot_num.numpy()])
    first_input = tf.expand_dims(first_input, axis=0)

music_text = []
for i in music:
    music_text.append(num_to_text[i])

print(''.join(music_text))
```


<br>

# 1. 악보 데이터 불러오기

+ `pianoabc.txt`는 클래식 피아노 곡의 멜로디만 들어있는 텍스트 파일로, 음표를 문자로 기록하기 위한 abc notation 형식으로 기록되어 있다.
    + `abc notation`: 음표는 알파벳, 음표의 길이는 숫자로 나타내어져 있다. 큰따옴표는 코드 표기 기호로 참고용 주석이라고 생각하면 된다. (따옴표 안의 코드들은 지워도 연주에 전혀 지장을 주지 않는다.)

```python
text = open('pianoabc.txt', 'r').read()

print(text)

------------------------------------------------------------
e"G"d2B"D"A3/2B/2c"G"B2G"D"A2e"G"d2B"D"A3/2B/2c:2/4"F"BF:3/4"G"G2e:"C"g2e"Bb"f2d"F"c2AF2e"C"g2e"Bb"f2d:2/4"F"cA:3/41"G"G2e:2"G"G2zA2:"D"a3/2b/2a ...
```

<br>

# 2. train 데이터셋 만들기

## 1) 문자 → 숫자 변환
+ 아이디어: 문자 데이터를 학습시킬 수 없으니 숫자로 바꿔주자
    + Step 1) 유니크한 단어들을 모아 놓는 `Bag of words` 리스트 만들기
    + Step 2) `Bag of words`를 기반으로 문자를 숫자화해주는 함수 또는 딕셔너리 만들기 (`text_to_num`)
    + Step 3) `text_to_num` 이용해서 원본데이터를 모두 숫자로 바꿔주기

```python
unique_text = list(set(text))
unique_text.sort()

print(unique_text)
print(len(unique_text))

------------------------------------------------------------
['"', "'", ',', '/', '1', '2', '3', '4', '5', '6', '7', '8', '9', ':', 'A', 'B', 'C', 'D', 'E', 'F', 'G', '^', '_', 'a', 'b', 'c', 'd', 'e', 'f', 'g', 'z']
31
```

```python
# utilities
text_to_num = {}
num_to_text = {}

for i, data in enumerate(unique_text):
    text_to_num[data] = i
    num_to_text[i] = data
```

```python
# original data → numbers
numbered_text = []
for i in text:
    numbered_text.append(text_to_num[i])

print(numbered_text)

------------------------------------------------------------
[27, 0, 20, 0, 26, 5, 15, 0, 17, 0, 14, 6, 3, 5, 15, 3, 5, 25, 0, 20, 0, 15, 5, 20, 0, 17, 0, 14, 5, 27, 0, 20, 0, 26, 5, 15, 0, 17, 0, 14, 6, 3, 5, 15, 3, 5, 25, 13, 5, 3, 7, 0, 19, 0, 15, 19, ...]
```

<br>

## 2) trainX, trainY 만들기
+ 아이디어: 원본데이터에서 25개의 연속된 문자 한 세트를 뽑아 첫번째 input data로 활용하자
    + step 1) 25개의 연속된 문자 한 세트를 trainX 데이터, 바로 다음에 오는 26번째 문자를 trainY 데이터로 뽑는다.
    + step 2) sequence 데이터를 만들어야하므로 원본데이터 전체를 이용해서 step1)을 반복하여 데이터를 만들어준다.
    + ex) 원본데이터: ABCDEFG~~~~
        + (ABCD, E), (BCDE, F), (CDEF, G), ...

```python
X = []
Y = []

for i in range(0, len(numbered_text)-25):
    X.append(numbered_text[i:i+25])
    Y.append(numbered_text[i+25])

print(X)
print(Y)

------------------------------------------------------------
[[27, 0, 20, 0, 26, 5, 15, 0, 17, 0, 14, 6, 3, 5, 15, 3, 5, 25, 0, 20, 0, 15, 5, 20, 0], [0, 20, 0, 26, 5, 15, 0, 17, 0, 14, 6, 3, 5, 15, 3, 5, 25, 0, 20, 0, 15, 5, 20, 0, 17], ...]
[17, 0, 14, 5, 27, 0, 20, 0, 26, 5, 15, 0, 17, 0, 14, 6, ...]
```

원핫인코딩해주자! (선택사항이지만 원핫인코딩해주면 성능이 더 잘 나오지 않을까?)

```python
import tensorflow as tf


X = tf.one_hot(X, 31)
Y = tf.one_hot(Y, 31)

print(X[:2])
print(Y[:2])

------------------------------------------------------------
tf.Tensor(
[[[0. 0. 0. ... 0. 0. 0.]
  [1. 0. 0. ... 0. 0. 0.]
  [0. 0. 0. ... 0. 0. 0.]
  ...
  [0. 0. 0. ... 0. 0. 0.]
  [0. 0. 0. ... 0. 0. 0.]
  [1. 0. 0. ... 0. 0. 0.]]

 [[1. 0. 0. ... 0. 0. 0.]
  [0. 0. 0. ... 0. 0. 0.]
  [1. 0. 0. ... 0. 0. 0.]
  ...
  [0. 0. 0. ... 0. 0. 0.]
  [1. 0. 0. ... 0. 0. 0.]
  [0. 0. 0. ... 0. 0. 0.]]], shape=(2, 25, 31), dtype=float32)

tf.Tensor(
[[0. 0. 0. 0. 0. 0. 0. 0. 0. 0. 0. 0. 0. 0. 0. 0. 0. 1. 0. 0. 0. 0. 0. 0.
  0. 0. 0. 0. 0. 0. 0.]
 [1. 0. 0. 0. 0. 0. 0. 0. 0. 0. 0. 0. 0. 0. 0. 0. 0. 0. 0. 0. 0. 0. 0. 0.
  0. 0. 0. 0. 0. 0. 0.]], shape=(2, 31), dtype=float32)
```

+ `tf.one_hot(tensor 또는 numpy.array 또는 list, 유니크한 문자 갯수)`
+ 유니크한 문자가 너무 많을 때는 원핫인코딩 대신 embedding layer를 쓴다.

<br>

# 3. 모델 빌딩 & 학습시키기
```python
model = tf.keras.models.Sequential([
    tf.keras.layers.LSTM(100, input_shape=(25, 31)),
    tf.keras.layers.Dense(31, activation='softmax')
])

model.compile(loss='categorical_crossentropy', optimizer='adam', metrics=['accuracy'])

model.fit(X, Y, batch_size=64, epochs=50, verbose=2)

model.save('model1')

------------------------------------------------------------
Epoch 1/50
4563/4563 - 69s - loss: 1.5950 - accuracy: 0.4970 - 69s/epoch - 15ms/step
...
Epoch 49/50
4563/4563 - 66s - loss: 0.7956 - accuracy: 0.7396 - 66s/epoch - 14ms/step
Epoch 50/50
4563/4563 - 65s - loss: 0.7938 - accuracy: 0.7401 - 65s/epoch - 14ms/step
```

+ `tf.keras.layers.LSTM(100, input_shape=(25, 31))`
    + 여기에 activation function 추가로 넣지 않아도 된다. LSTM 모델 안에 이미 activation function이 많이 들어가 있기 때문.
    + `input_shape`에는 역시 데이터 1개의 shape 써주기.
    + LSTM을 2개, 3개 중첩해서 넣을 수도 있다. 이럴 때는 `return_sequences=True` 파라미터가 추가로 필요하다. 그래야 LSTM을 거치고나서 sequence를 하나 더 내보내고 LSTM에 넣을 수 있다.
+ `tf.keras.layers.Dense(31, activation='softmax')`
    + 카테고리 예측 문제. 문자 31개 중 가장 높은 확률을 보이는 문자를 26번째 문자로 예측.
+ `activation='softmax'`, `loss='categorical_crossentropy'`
    + 이 둘은 언제나 세트! 카테고리 예측 문제에서는 언제나 이 둘을 같이 써준다.
    + 만약 데이터가 원핫인코딩이 안 되어 있고, 정수로 되어있다면 `loss='sparse_categorical_crossentropy'` 써주기.
+ `modell.fit(X, Y, batch_size=64, epochs=30, verbose=2)`
    + `batch_size=64`: 모든 데이터를 한꺼번에 넣고 w값 업데이트 일어나게 하고 싶다면 이 파라미터는 넣지 않는다. batch size를 64로 설정해주면, 64개 데이터를 학습한 후에 w값 업데이트가 일어난다.
    + LSTM은 epoch을 많이 돌려줘야 성능이 어느 정도 나온다. (최소 30 epochs 이상)
    + 이 프로젝트에서 validation은 굳이 필요하지 않아 생략.
    + `verbose=2`: 학습 중 출력되는 내용을 줄여준다.
        + Epoch이 돌면서 'loss', 'accuracy'가 출력되는데 epoch이 길어질수록 이러한 출력이 많아지면서 가끔 학습이 끊길 때가 있다. 과부하를 방지해주기 위해 출력되는 내용을 줄여준다. (특히 Google Colab에서 30 epochs 이상 학습 시 필수..!)

<br>

# 4. 예측하기

## 1) 모델 불러오기

```python
# load model
model = tf.keras.models.load_model('model1')
```
+ predict하기 위한 데이터셋도 train dataset 만들 때와 마찬가지로 똑같이 전처리를 해줘야 한다. 본 프로젝트에서는 따로 predict용 데이터가 없으므로 train dataset 중에서 임의로 25개의 연속된 문자를 뽑아 그 뒤를 sequence하게 예측해서 곡을 완성해보자. (내가 임의로 첫 25개 문자를 만들어서 작곡할 음악적 감각은 없으므로!)

<br>

## 2) Prediction을 위한 input data 만들기
```python
# make first input data for prediction
first_input = numbered_text[117:117+25]     # randomly chose 117
first_input = tf.one_hot(first_input, 31)   # shape=(25, 31)
first_input = tf.expand_dims(first_input, axis=0)

print(first_input)

------------------------------------------------------------
tf.Tensor(
[[[0. 0. 0. 0. 0. 0. 0. 0. 0. 0. 0. 0. 0. 0. 0. 0. 0. 0. 0. 0. 1. 0. 0.
   0. 0. 0. 0. 0. 0. 0. 0.]
...
  [0. 0. 0. 1. 0. 0. 0. 0. 0. 0. 0. 0. 0. 0. 0. 0. 0. 0. 0. 0. 0. 0. 0.
   0. 0. 0. 0. 0. 0. 0. 0.]]], shape=(1, 25, 31), dtype=float32)
```

<br>

## 3) 예측하기

```python
import numpy as np


# predict 26th letter
pred_val = model.predict(first_input)

pred_val = np.argmax(pred_val[0])

print(pred_val)                 # predicted value
print(num_to_text[pred_val])    # predicted value → original data
print(numbered_text[117+25])    # actual value

------------------------------------------------------------
1/1 [==============================] - 0s 263ms/step
5
2
5
```
+ `tf.expand_dims(first_input, axis=0)`
    + 학습할 때 넣은 shape인 (29만, 25, 31)과 shape를 동일하게 맞춰주기 위해 `(25,31) → (1,25,31)`로 바꿔준다.
+ `np.argmax(pred_val[0])` : 31개 예측값 확률 리스트에서 최댓값 인덱스 출력
+ `pred_val` 값과 `numbered_text[117+_25]` 값이 동일하게 나오는 것으로 보아 모델이 정확하게 예측하고 있음을 알 수 있다.


이제 200개의 음표를 연속 예측해보자.
+ 아이디어: 첫 입력값을 이용해 다음 값을 예측하고, 그 다음 입력값을 만들어 반복적으로 연속하여 원하는 갯수만큼 예측하기
    + Step 1) first input data 만들기 (위에서 진행 완료)
    + Step 2) 다음에 올 문자 예측
    + Step 3) first input data에서 첫 문자 제거
    + Step 4) 예측한 문자를 뒤에 붙이기
    + Step 5) 원핫인코딩, 차원 조정
    + Step 2 ~ 5) 원하는 예측 갯수만큼 반복

```python
music = []
for i in range(200):
    pred_val = model.predict(first_input)
    pred_val = np.argmax(pred_val[0])

    music.append(pred_val)

    next_input = first_input[0][1:]
    one_hot_num = tf.one_hot(pred_val, 31)

    first_input = np.vstack([next_input, one_hot_num.numpy()])
    first_input = tf.expand_dims(first_input, axis=0)

print(music)

------------------------------------------------------------
[5, 23, 29, 0, 14, 0, 25, 3, 5, 27, 3, 5, 27, 3, 5, 25, 3, 5, 0, 18, 10, 0, 15, 3, 5, 14, 3, 5, 20, 3, 5, 15, 3, 5, 0, 14, 0, 14, 3, 5, ...]
```

```python
# predicted value (number) → original data
music_text = []
for i in music:
    music_text.append(num_to_text[i])

print(''.join(music_text))

------------------------------------------------------------
2ag"A"c/2e/2e/2c/2"E7"B/2A/2G/2B/2"A"A/2B/2A/2G/2E/2E/2F/2G/2"A"A/2B/2c/2A/2"G"G/2B/2A/2G/2G/2G/2A/2G/2"D"A/2B/2A/2G/2F/2D/2F/2A/2"G"B/2G/2B/2d/2"A7"e/2d/2e/2c/2"D"d/2e/2f/2d/2A/2B/2A/2"G"G/2A/2B/2c/2
```

<br>

## 4) (+ plus!!) 예측에 변칙주기

예측을 하면서 가끔씩 예측이 일정한 패턴의 루프에 갇힐 수도 있다. 예를 들어 '도레미파솔, 도레미파솔, 도레미파솔, ....' 무한반복의 음악이 작곡될 수도 있다. 이런 일정한 루프에 갇히는 것을 방지하기 위해 예측에 변칙을 줄 수 있다.

+ 아이디어: 확률이 가장 높은 것으로만 뽑지말고, 확률대로 뽑기
    + `np.random.choice` 함수를 사용해서 `unique_text`와 `pred_val`에 매칭되는 확률값대로 예측값 추출

```python
music = []
for i in range(200):
    pred_val = model.predict(first_input)
    pred_val = np.random.choice(unique_text, 1, p=pred_val[0])
    pred_val = text_to_num[pred_val[0]]

    music.append(pred_val)

    next_input = first_input.numpy()[0][1:]

    one_hot_num = tf.one_hot(pred_val, 31)

    first_input = np.vstack([next_input, one_hot_num.numpy()])
    first_input = tf.expand_dims(first_input, axis=0)

print(music)

------------------------------------------------------------
[5, 23, 24, 0, 17, 0, 23, 3, 5, 29, 28, 3, 5, 27, 3, 5, 28, 3, 5, 0, 18, 0, 29, 29, 3, 5, 23, 3, 5, 24, 3, 5, 29, 3, 5, 27, 0, 14, 0, 26, 3, 5, 25, 3, 5, ...]
```
```python
# predicted value (number) → original data
music_text = []
for i in music:
    music_text.append(num_to_text[i])

print(''.join(music_text))

------------------------------------------------------------
2ab"D"a/2gf/2e/2f/2"E"gg/2a/2b/2g/2e"A"d/2c/2e/2c/2Ac/2e/2"D"f/2c/2d/2e/2"B"d/2c/2B/2c/2"E"ee"E7"BA"A7"cedc"D"d2ef"A7"gfed"A"caec"A"eaaf"A"e3/2c/2"E7"A2"A"AA/2c/2EB/2^d/2"A"ce/2d/2e/2f/2g/2g/2A/2a/2e/
```

<br>

# 5. 결과
+ ABC notation 플레이어에서 내가 만든 작곡AI 모델로 예측하여 생산한 곡을 플레이해보자!
    + http://www.tradtunedb.org.uk/#/editor
    + http://www.mandolintab.net/abcconverter.php
    + http://abcnotation.com/software#elm-ab (윈도우/맥 설치용 소프트웨어)
+ 음악이 스케일의 구성음 안에서 음들이 조화롭게 작곡되었다.
+ 주석의 규칙도 잘 학습한 것으로 나타났다. 다만, 따옴표가 홀수개가 나와 주석처리가 제대로 안 될 경우에 대비해서 따옴표가 홀수인 경우 마지막 따옴표 이후 글자는 모두 제거해주는 코드를 추가해줘도 좋을 듯 하다.

<br>

# Reference
+ [코딩애플 - 문과여도 상관없다 Tensorflow 딥러닝 AI 기초부터 실무까지](https://codingapple.com/course/python-deep-learning/)