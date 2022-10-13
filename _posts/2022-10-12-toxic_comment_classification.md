---
title: "[DL/NLP/PRJ] 댓글 검열 AI 만들기"
excerpt: ""
categories:
  - DeepLearning
  - NLP
  - Project
tags:
  - sequence
  - coding apple
last_modified_at: 2022-10-12
---

> 악플/선플을 판단하는 AI를 만들어보자.

# Code
```python
import pandas as pd
import numpy as np
from tensorflow.keras.preprocessing.text import Tokenizer
from tensorflow.keras.preprocessing.sequence import pad_sequences
from skleran.model_selection import train_test_split


raw = pd.read_table('shopping.txt', names=['ratings', 'reviews'])

raw['label'] = np.where(raw['rating'] > 3, 1, 0)

# data-preprocessing
raw['reviews'] = raw['reviews'].str.replace('[^ㄱ-ㅎㅏ-ㅣ가-힣0-9 ]', '')

# print(raw.isnull().sum())

raw.drop_duplicates(subset=['review'], inplace=True)

comments_list = raw['reviews'].tolist()
unique_letters = ''.join(comments_list)
unique_letters = list(set(unique_letters))
unique_letters.sort()

tokenizer = Tokenizer(char_level=True, oov_token='<OOV>')
tokenizer.fit_on_texts(comments_list)
train_seq = tokenizer.texts_to_sequences(comments_list)

raw['length'] = raw['reviews'].str.len()

# print(raw.describe())
# raw['length'][raw['length'] < 100].count()

X = pad_sequences(train_seq, maxlen=100)
Y = raw['label'].tolist()

# dataset split
trainX, valX, trainY, valY = train_test_split(X, Y, test_size=0.2, random_state=42)
```
```python
import tensorflow as tf


# model
model = tf.keras.models.Sequential([
    tf.keras.layers.Embedding(len(unique_letters)+1, 16, input_shape=(100,)),
    tf.keras.layers.LSTM(100),
    tf.keras.layers.Dense(1, activation='sigmoid')
])

model.summary()

# train
model.compile(optimizer='adam', loss='binary_crossentropy', metrics=['accuracy'])
model.fit(trainX, np.array(trainY), validation_data=(valX, np.array(valY)), epochs=5)
```

<br>

# 1. 데이터 로드 및 정답 데이터 라벨링
+ 어떤 데이터를 사용할까?
    + AI에게 악플/선플을 구분하여 학습시킬 데이터로 네이버 쇼핑 후기 데이터를 사용할 것이다. 네이버 쇼핑 후기 데이터에서 별점 1~2점은 악플로, 4~5점은 선플로 구분하여 라벨링을 해서 진행하자. (본 데이터에 별점 3점의 데이터는 제외되어있다.)

```python
import pandas as pd
import numpy as np


raw = pd.read_table('shopping.txt', names=['ratings', 'reviews'])

print(raw)
------------------------------------------------------------------------------------------
        ratings                                            reviews
0             5                                            배공빠르고 굿
1             2                      택배가 엉망이네용 저희집 밑에층에 말도없이 놔두고가고
2             5  아주좋아요 바지 정말 좋아서2개 더 구매했어요 이가격에 대박입니다. 바느질이 조금 ...
3             2  선물용으로 빨리 받아서 전달했어야 하는 상품이었는데 머그컵만 와서 당황했습니다. 전...
4             5                  민트색상 예뻐요. 옆 손잡이는 거는 용도로도 사용되네요 ㅎㅎ
...         ...                                                ...
199995        2                                    장마라그런가!!! 달지않아요
199996        5  다이슨 케이스 구매했어요 다이슨 슈퍼소닉 드라이기 케이스 구매했어요가격 괜찮고 배송...
199997        5                    로드샾에서 사는것보다 세배 저렴하네요 ㅜㅜ 자주이용할께요
199998        5                                      넘이쁘고 쎄련되보이네요~
199999        5   아직 사용해보지도않았고 다른 제품을 써본적이없어서 잘 모르겠지만 ㅎㅎ 배송은 빨랐습니다

[200000 rows x 2 columns]
```

```python
raw['label'] = np.where(raw['rating'] > 3, 1, 0)

print(raw)
------------------------------------------------------------------------------------------
        ratings                                            reviews  label
0             5                                            배공빠르고 굿      1
1             2                      택배가 엉망이네용 저희집 밑에층에 말도없이 놔두고가고      0
2             5  아주좋아요 바지 정말 좋아서2개 더 구매했어요 이가격에 대박입니다. 바느질이 조금 ...      1
3             2  선물용으로 빨리 받아서 전달했어야 하는 상품이었는데 머그컵만 와서 당황했습니다. 전...      0
4             5                  민트색상 예뻐요. 옆 손잡이는 거는 용도로도 사용되네요 ㅎㅎ      1
...         ...                                                ...    ...
199995        2                                    장마라그런가!!! 달지않아요      0
199996        5  다이슨 케이스 구매했어요 다이슨 슈퍼소닉 드라이기 케이스 구매했어요가격 괜찮고 배송...      1
199997        5                    로드샾에서 사는것보다 세배 저렴하네요 ㅜㅜ 자주이용할께요      1
199998        5                                      넘이쁘고 쎄련되보이네요~      1
199999        5   아직 사용해보지도않았고 다른 제품을 써본적이없어서 잘 모르겠지만 ㅎㅎ 배송은 빨랐습니다      1

[200000 rows x 3 columns]
```
+ `np.where(raw['rating'] > 3, 1, 0)`
    + `ratings`가 3 이상이면 1(선플)로 라벨링, 그렇지 않으면 0(악플)으로 라벨링 (label 데이터 만들기!)
    + 조건에 따라서 새로운 열을 생성할 때 쓰는 문구

<br>

# 2. 한글 데이터 전처리

## 1) trainX 데이터 만들기
+ 모델에 데이터를 넣기 위해서는 `(input text, label)` 형태로 만들어서 넣어야할텐데 한글을 어떻게 숫자로 바꿀까? <u><b>단어 단위 vs. 글자 단위?</b></u>
    + 정제되지 않은 한글은 문자 단위로 숫자화하는게 좋다. 유니크한 단어만 세어도 몇십만개로 너무 많이 나오기 때문! (특히 오타, 신조어가 많은 말뭉치인데 그대로 놔둘 경우에 유니크한 단어는 더 많아진다.)

### (1) 한글이 아닌 문자 제거
```python
raw['reviews'] = raw['reviews'].str.replace('[^ㄱ-ㅎㅏ-ㅣ가-힣0-9 ]', '')
```
+ 지금은 한글 데이터를 처리하고 있으니 영어와 특수문자 등 불필요한 문자를 제거해주자. 이때 정규식을 이용하면 편하다.
+ `'[^ㄱ-ㅎㅏ-ㅣ가-힣0-9 ]'`
    + 한글로 표현할 수 있는 모든 문자들
    + 스페이스도 포함해 줘야 한다. 그래야 띄어쓰기도 포함해서 전처리 된다.
    + 위 코드는 이 문자들에 해당되지 않는다면 공백으로 대체하라는 코드
+ 만약 영어도 포함시키고 싶다면 ⇒ `'[^ㄱ-ㅎㅏ-ㅣ가-힣0-9A-Za-z ]'`

<br>

### (2) 공백 제거 (결측치 처리)
```python
print(raw.isnull().sum())

------------------------------------------------------------
ratings    0
reviews    0
label      0
dtype: int64
```
+ 결측치가 있는지 확인해주었지만 이 데이터에 결측치가 없는 것으로 확인! 따라서 넘어가도록 하자.

<br>

### (3) 중복 데이터 제거
+ 똑같은 리뷰인데 평점이 다르면 혼란을 줄 수 있으니 제거한다.

```python
raw.drop_duplicates(subset=['review'], inplace=True)

print(raw)

------------------------------------------------------------------------------------------
        ratings                                            reviews  label
0             5                                            배공빠르고 굿      1
1             2                      택배가 엉망이네용 저희집 밑에층에 말도없이 놔두고가고      0
2             5  아주좋아요 바지 정말 좋아서2개 더 구매했어요 이가격에 대박입니다 바느질이 조금 엉...      1
3             2  선물용으로 빨리 받아서 전달했어야 하는 상품이었는데 머그컵만 와서 당황했습니다 전화...      0
4             5                   민트색상 예뻐요 옆 손잡이는 거는 용도로도 사용되네요 ㅎㅎ      1
...         ...                                                ...    ...
199995        2                                       장마라그런가 달지않아요      0
199996        5  다이슨 케이스 구매했어요 다이슨 슈퍼소닉 드라이기 케이스 구매했어요가격 괜찮고 배송...      1
199997        5                    로드샾에서 사는것보다 세배 저렴하네요 ㅜㅜ 자주이용할께요      1
199998        5                                       넘이쁘고 쎄련되보이네요      1
199999        5   아직 사용해보지도않았고 다른 제품을 써본적이없어서 잘 모르겠지만 ㅎㅎ 배송은 빨랐습니다      1

[199425 rows x 3 columns]
```

<br>

### (4) 유니크한 문자 모으기 (BoW)
+ 글자를 정수로 바꾸기 위해 유니크한 문자를 모으자! (`Bag of Words` 만들기)

```python
comments_list = raw['reviews'].tolist()

print(unique_letters)

------------------------------------------------------------
['배공빠르고 굿',
 '택배가 엉망이네용 저희집 밑에층에 말도없이 놔두고가고',
 '아주좋아요 바지 정말 좋아서2개 더 구매했어요 이가격에 대박입니다 바느질이 조금 엉성하긴 하지만 편하고 가성비 최고예요',
 '선물용으로 빨리 받아서 전달했어야 하는 상품이었는데 머그컵만 와서 당황했습니다 전화했더니 바로주신다했지만 배송도 누락되어있었네요 확인안하고 바로 선물했으면 큰일날뻔했네요이렇게 배송이 오래걸렸으면 사는거 다시 생각했을거같아요 아쉽네요',
 '민트색상 예뻐요 옆 손잡이는 거는 용도로도 사용되네요 ㅎㅎ', ...]
```

```python
unique_letters = ''.join(comments_list)
unique_letters = list(set(unique_letters))
unique_letters.sort()

print(unique_letters)

------------------------------------------------------------
[' ', '0', '1', '2', '3', '4', '5', '6', '7', '8', '9', 'ㄱ', 'ㄲ', 'ㄳ', 'ㄴ', 'ㄵ', 'ㄶ', 'ㄷ', ...]
```

<br>

### (5) 문자 to 정수 (Tokenizer)

이제 문자들을 정수로 바꿔주자.

```python
from tensorflow.keras.preprocessing.text import Tokenizer


tokenizer = Tokenizer(char_level=True, oov_token='<OOV>')
tokenizer.fit_on_texts(comments_list)

print(tokenizer.word_index)

------------------------------------------------------------
{'<OOV>': 1, ' ': 2, '요': 3, '이': 4, '고': 5, '다': 6, '아': 7, '는': 8, '어': 9, '하': 10, '니': 11, '가': 12, '서': 13, '도': 14, '지': 15, '네': 16, '에': 17, '데': 18, '좋': 19, '구': 20, ..., '즋': 3207, '퐄': 3208, '쭁': 3209, '듿': 3210, '눛': 3211}
```

+ `text_to_num`과 같은 함수 또는 딕셔너리를 만들어도 되지만, Tensorflow에는 이를 자동으로 해주는 함수가 있다 ⇒ `Tokenizer()`
+ `tokenizer = Tokenizer(char_level=True, oov_token='<OOV>')`
    + `char_level=True` : 글자단위를 정수로 변환하겠다
    + `char_level=False` : 단어단위(띄어쓰기 단위)를 정수로 변환하겠다
    + `oov_token='<OOV>'`
        + 기존에 train 데이터를 정수화했는데 val/test 데이터를 다룰 때 새로운 글자가 출현할 가능성이 있다. 처음 보는 글자/단어를 어떻게 치환할지를 `<OOV>`에 써준 것
        + OOV : Out of Vocabulary
        + 꼭 OOV가 아니더라도 없는 단어를 뜻하는 아무 말이나 써도 된다.
        + 구분할 수 있게 <>와 같은 특수기호를 써주는 것이 좋다. (이미 OOV라는 단어가 있을 수도 있으니까!)
+ `tokenizer.fit_on_texts(data를 list로 바꾼 것)`
    + data를 list로 바꿔서 넣어주면 list에 있는 모든 글자/단어들을 정수로 바꿔준다.
+ `tokenizer.word_index`
    + `text_to_num` 딕셔너리를 만드는 것과 동일한 기능!
    + 프린트 된 결과를 보니 본 데이터에는 3천자 정도가 나왔다. 실제 사용하는 한글 문자는 2천자 정도라고 하니 이 데이터에는 오타가 많이 포함이 된 듯하다. 오타를 전처리하는 방법 중에 단어 단위로 처리할 경우 전체 데이터 중 1회 이하 출현하는 단어를 index에서 삭제해 주는 방법이 있다. 오타 하나 삭제했다고 문장의 큰 의미를 바꾸지 않으므로 이런 방법으로 모델의 성능이 올라가기도 한다. (ex. 안령하세요 → 삭제!)
+ (참고) Tokenizing 할 때 train/val 데이터 구분없이 모든 데이터를 한꺼번에 tokenizer에 넣어서 `tokenizer.fit_on_texts`를 돌려주었지만, 때에 따라 val 데이터를 제외해도 train 데이터셋만 tokenizing을 하기도 한다.

<br>

이제 train 데이터셋 전체를 숫자로 변환해주자!

```python
train_seq = tokenizer.texts_to_sequences(comments_list)

print(comments_list)
print(train_seq)

------------------------------------------------------------
['배공빠르고 굿', '택배가 엉망이네용 저희집 밑에층에 말도없이 놔두고가고', '아주좋아요 바지 정말 좋아서2개 더 구매했어요 이가격에 대박입니다 바느질이 조금 엉성하긴 하지만 편하고 가성비 최고예요', '선물용으로 빨리 받아서 전달했어야 하는 상품이었는데 머그컵만 와서 당황했습니다 전화했더니 바로주신다했지만 배송도 누락되어있었네요 확인안하고 바로 선물했으면 큰일날뻔했네요이렇게 배송이 오래걸렸으면 사는거 다시 생각했을거같아요 아쉽네요', '민트색상 예뻐요 옆 손잡이는 거는 용도로도 사용되네요 ㅎㅎ', ...]
[[40, 324, 89, 88, 5, 2, 298], [292, 40, 12, 2, 401, 220, 4, 16, 47, 2, 84, 561, 219, 2, 514, 17, 880, 17, 2, 133, 14, 68, 4, 2, 658, 110, 5, 12, 5], [7, 38, 19, 7, 3, 2, 113, 15, 2, 62, 133, 2, 19, 7, 13, 166, 103, 2, 76, 2, 20, 31, 43, 9, 3, 2, 4, 12, 114, 17, 2, 67, 224, 53, 11, 6, 2, 113, 142, 134, 4, 2, 94, 155, 2, 401, 158, 10, 212, 2, 10, 15, 26, 2, 92, 10, 5, 2, 12, 158, 64, 2, 261, 5, 129, 3], [161, 98, 47, 49, 25, 2, 181, 33, 2, 131, 7, 13, 2, 75, 173, 43, 9, 122, 2, 10, 8, 2, 54, 48, 4, 101, 8, 18, 2, 269, 34, 662, 26, 2, 177, 13, 2, 186, 530, 43, 44, 11, 6, 2, 75, 184, 43, 76, 11, 2, 113, 25, 38, 117, 6, 43, 15, 26, 2, 40, 46, 14, 2, 368, 321, 74, 9, 30, 101, 16, 3, 2, 249, 57, 36, 10, 5, 2, 113, 25, 2, 161, 98, 43, 49, 59, 2, 340, 80, 229, 690, 43, 16, 3, 4, 268, 32, 2, 40, 46, 4, 2, 95, 112, 147, 206, 49, 59, 2, 21, 8, 41, 2, 6, 45, 2, 87, 105, 43, 52, 41, 51, 7, 3, 2, 7, 258, 16, 3], [384, 149, 119, 54, 2, 129, 246, 3, 2, 564, 2, 223, 304, 4, 8, 2, 41, 8, 2, 47, 14, 25, 14, 2, 21, 47, 74, 16, 3, 2, 82, 82], ...]
```

<br>

### (6) 모델에 들어갈 문장 길이 제한하기

모델에는 길이가 같은 문장들만 집어넣을 수 있다. 문장의 최대길이를 정해주고 빈 공간은 0으로 padding해주면 된다. <br>
그럼 우선 우리 데이터에서 제일 긴 문장이 어떻게 되는지 데이터를 파악해보자.

```python
raw['length'] = raw['reviews'].str.len()

print(raw.head())

--------------------------------------------------------------------------------
   ratings                                            reviews  label  length
0        5                                            배공빠르고 굿      1       7
1        2                      택배가 엉망이네용 저희집 밑에층에 말도없이 놔두고가고      0      29
2        5  아주좋아요 바지 정말 좋아서2개 더 구매했어요 이가격에 대박입니다 바느질이 조금 엉...      1      66
3        2  선물용으로 빨리 받아서 전달했어야 하는 상품이었는데 머그컵만 와서 당황했습니다 전화...      0     129
4        5                   민트색상 예뻐요 옆 손잡이는 거는 용도로도 사용되네요 ㅎㅎ      1      32
```

```python
print(raw.describe())

------------------------------------------------------------
             ratings          label         length
count  199425.000000  199425.000000  199425.000000
mean        3.227088       0.500404      38.061432
std         1.645598       0.500001      28.049906
min         1.000000       0.000000       1.000000
25%         2.000000       0.000000      17.000000
50%         4.000000       1.000000      28.000000
75%         5.000000       1.000000      53.000000
max         5.000000       1.000000     140.000000
```
+ length의 max가 140인 것으로 확인! 모든 문장을 max length로 맞추기 보다는 100~120 글자수로 맞춰도 좋을 듯하다. 그러면 length가 100 미만인 글자수를 보이는 데이터 갯수를 세보자.
+ 추가로, label 통계에서 선플(1)과 악플(0) 데이터의 평균이 0.5로 나타난 것으로 보아, 선플과 악플 데이터가 각각 반 정도씩 잘 나뉘어 있는 것도 확인!

<br>

length가 100 미만인 것만 세주기
```python
raw['length'][raw['length'] < 100].count()

------------------------------------------------------------
190496
```
+ length가 100 미만인 것만 세주니 총 199,425개 중 190,496개로 나타났다. 적당해 보이므로 max length를 100으로 설정하고 진행하자.

<br>

모든 문장 길이 100자로 제한하고, 100자 미달시 0으로 채우기(padding)
```python
from tensorflow.keras.preprocessing.sequence import pad_sequences


X = pad_sequences(train_seq, maxlen=100)

print(X)
print(X[0])

------------------------------------------------------------
array([[  0,   0,   0, ...,   5,   2, 298],
       [  0,   0,   0, ...,   5,  12,   5],
       [  0,   0,   0, ...,   5, 129,   3],
       ...,
       [  0,   0,   0, ..., 102, 256,   3],
       [  0,   0,   0, ...,   4,  16,   3],
       [  0,   0,   0, ...,  44,  11,   6]], dtype=int32)

array([  0,   0,   0,   0,   0,   0,   0,   0,   0,   0,   0,   0,   0,
         0,   0,   0,   0,   0,   0,   0,   0,   0,   0,   0,   0,   0,
         0,   0,   0,   0,   0,   0,   0,   0,   0,   0,   0,   0,   0,
         0,   0,   0,   0,   0,   0,   0,   0,   0,   0,   0,   0,   0,
         0,   0,   0,   0,   0,   0,   0,   0,   0,   0,   0,   0,   0,
         0,   0,   0,   0,   0,   0,   0,   0,   0,   0,   0,   0,   0,
         0,   0,   0,   0,   0,   0,   0,   0,   0,   0,   0,   0,   0,
         0,   0,  40, 324,  89,  88,   5,   2, 298], dtype=int32)
```
+ `pad_sequences(train_seq, maxlen=100)`: padding할 때 0을 앞에서부터 채울지 뒤에 채울지 설정 가능. 보통은 앞에 넣어준다.

<br>

## 2) trainY 데이터 만들기

정답 데이터를 모델에 넣기 좋게 리스트 형태로 바꾸자.

```python
Y = raw['label'].tolist()

print(Y)

------------------------------------------------------------
[1, 0, 1, 0, 1, 0, 0, 0, 0, 0, 1, ...]
```

<br>

## 3) train/val 데이터 분리

```python
from skleran.model_selection import train_test_split


trainX, valX, trainY, valY = train_test_split(X, Y, test_size=0.2, random_state=42)

print(len(trainX))
print(len(valX))

------------------------------------------------------------
159540
39885
```

이렇게 모델에 넣을 trainX, trainY, valX, valY 데이터가 모두 완성되었다!

<br>

# 3. 모델 빌딩

```python
import tensorflow as tf


model = tf.keras.models.Sequential([
    tf.keras.layers.Embedding(len(unique_letters)+1, 16, input_shape=(100,)),
    tf.keras.layers.LSTM(100),
    tf.keras.layers.Dense(1, activation='sigmoid')
])

model.summary()

------------------------------------------------------------
Model: "sequential_1"
_________________________________________________________________
 Layer (type)                Output Shape              Param #   
=================================================================
 embedding_1 (Embedding)     (None, 100, 16)           51376     
                                                                 
 lstm_1 (LSTM)               (None, 100)               46800     
                                                                 
 dense_1 (Dense)             (None, 1)                 101       
                                                                 
=================================================================
Total params: 98,277
Trainable params: 98,277
Non-trainable params: 0
_________________________________________________________________
```
+ `tf.keras.layers.Embedding(len(unique_letters)+1, 16)`
    + 원핫인코딩을 하려 했더니 유니크한 문자가 3천개가 넘어갔다. 이렇게 유니크한 문자가 너무 많은 경우에는 `Embedding layer`를 쓰면 된다! 즉, `Embedding layer`는 원핫인코딩 대신 쓸 수 있는 레이어
    + `len(unique_letters)+1`: 유니크한 글자 갯수에 `<OOV>` 1개도 포함한 갯수
    + `16`: 한 글자를 16개의 숫자가 담긴 vector(행렬)로 바꿔준다.
    + 1,2,3 이렇게 정수로 변환하는 것보다 Embedding layer처럼 복잡한 vector로 변환하면 결과가 더 좋을 수 있다.
    + 처음에는 0~1 사이의 무작위 숫자로 변환하지만 학습이 진행될수록 최적의 vector 값을 찾아서 글자에 대입해준다.

<br>

# 4. 학습하기

```python
model.compile(optimizer='adam', loss='binary_crossentropy', metrics=['accuracy'])
model.fit(trainX, np.array(trainY), validation_data=(valX, np.array(valY)), epochs=5)

------------------------------------------------------------
Epoch 1/5
4986/4986 [==============================] - 178s 35ms/step - loss: 0.3038 - accuracy: 0.8821 - val_loss: 0.2746 - val_accuracy: 0.8963
Epoch 2/5
4986/4986 [==============================] - 175s 35ms/step - loss: 0.2508 - accuracy: 0.9054 - val_loss: 0.2385 - val_accuracy: 0.9107
Epoch 3/5
4986/4986 [==============================] - 175s 35ms/step - loss: 0.2317 - accuracy: 0.9141 - val_loss: 0.2307 - val_accuracy: 0.9162
Epoch 4/5
4986/4986 [==============================] - 175s 35ms/step - loss: 0.2190 - accuracy: 0.9194 - val_loss: 0.2246 - val_accuracy: 0.9164
Epoch 5/5
4986/4986 [==============================] - 175s 35ms/step - loss: 0.2094 - accuracy: 0.9235 - val_loss: 0.2220 - val_accuracy: 0.9195
```
+ Validation accuracy가 약 92% 정도로 모델이 좋은 성능을 보였다!!😀

<br>

# Reference
+ [코딩애플 - 문과여도 상관없다 Tensorflow 딥러닝 AI 기초부터 실무까지](https://codingapple.com/course/python-deep-learning/)