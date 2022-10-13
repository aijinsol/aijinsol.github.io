---
title: "[DL/PRJ] Feature Columns"
excerpt: "타이타닉 생존자 찾기"
categories:
  - DeepLearning
  - Project
tags:
  - tensorflow
  - feature columns
  - coding apple
last_modified_at: 2022-10-13
---

> 데이터셋에 컬럼이 많은 경우 전처리를 효율적으로 한번에 하기 위해 `feature columns`를 활용하여 전처리를 자동화하는 것이 필수!

| 타이타닉 생존자 예측하기 (사람의 정보를 입력했을 때 이 사람의 생존확률 예측하기)

# Code
```python
import pandas as pd
import tensorflow as tf


# Load dataset
data = pd.read_csv('train.csv')
# print(data.isnull().sum())

age_mean = data['Age'].mean()
embarked_mode = data['Embarked'].mode()

data['Age'].fillna(value=round(age_mean), inplace=True)
data['Embarked'].fillna(value=embarked_mode.values[0], inplace=True)

ans = data.pop('Survived')

ds = tf.data.Dataset.from_tensor_slices( (dict(data), ans) )
ds_batch = ds.batch(32)

# Feature columns
feature_columns = []

## 숫자 그대로: Fare, SibSp, Parch
feature_columns.append(tf.feature_column.numeric_column('Fare'))
feature_columns.append(tf.feature_column.numeric_column('SibSp'))
feature_columns.append(tf.feature_column.numeric_column('Parch'))

## 숫자를 범위로 묶어서 카테고리화: Age
Age = tf.feature_column.numeric_column('Age')
Age_bucket = tf.feature_column.bucketized_column(Age, boundaries=[10,20,30,40,50,60,70])
feature_columns.append(Age_bucket)

## 카테고리 원핫인코딩: Sex, Embarked, Pclass
cat_columns = ['Sex', 'Embarked', 'Pclass']

for i in cat_columns:
    vocab = data[i].unique()
    cat = tf.feature_column.categorical_column_with_vocabulary_list(i, vocab)
    one_hot = tf.feature_column.indicator_column(cat)
    feature_columns.append(one_hot)

## 많은 카테고리 embedding: Ticket
vocab = data['Ticket'].unique()
cat = tf.feature_column.categorical_column_with_vocabulary_list('Ticket', vocab)
one_hot = tf.feature_column.embedding_column(cat, dimension=10)
feature_columns.append(one_hot)

# Model
model = tf.keras.Sequential([
    tf.keras.layers.DenseFeatures(feature_columns),
    tf.keras.layers.Dense(128, activation='relu'),
    tf.keras.layers.Dense(64, activation='relu'),
    tf.keras.layers.Dropout(0.2),
    tf.keras.layers.Dense(1, activation='sigmoid')
])

model.compile(optimizer='adam', loss='binary_crossentropy', metrics=['acc'])

# Train
model.fit(ds_batch, shuffle=True, epochs=30)
```

<br>

# 1. Data Preprocessing

## 1) 데이터 로드
```python
import pandas as pd


data = pd.read_csv('train.csv')

print(data)

------------------------------------------------------------
     PassengerId  Survived  Pclass  \
0              1         0       3   
1              2         1       1   
2              3         1       3   
3              4         1       1   
4              5         0       3   
..           ...       ...     ...   
  
                                                  Name     Sex   Age  SibSp  \
0                              Braund, Mr. Owen Harris    male  22.0      1   
1    Cumings, Mrs. John Bradley (Florence Briggs Th...  female  38.0      1   
2                               Heikkinen, Miss. Laina  female  26.0      0   
3         Futrelle, Mrs. Jacques Heath (Lily May Peel)  female  35.0      1   
4                             Allen, Mr. William Henry    male  35.0      0   
..                                                 ...     ...   ...    ...   
 
     Parch            Ticket     Fare Embarked  
0        0         A/5 21171   7.2500        S  
1        0          PC 17599  71.2833        C  
2        0  STON/O2. 3101282   7.9250        S  
3        0            113803  53.1000        S  
4        0            373450   8.0500        S  
..     ...               ...      ...      ...  

[891 rows x 11 columns]
```
<br>

## 2) 결측치 처리

우선 데이터에 결측치가 있는지 확인해주자.

```python
print(data.isnull().sum())

------------------------------------------------------------
PassengerId      0
Survived         0
Pclass           0
Name             0
Sex              0
Age            177
SibSp            0
Parch            0
Ticket           0
Fare             0
Embarked         2
dtype: int64
```

<br>

`Age`의 경우 빈칸을 평균으로, `Embarked`는 빈칸을 최빈값으로 채워주자.

```python
age_mean = data['Age'].mean()
embarked_mode = data['Embarked'].mode()

print(age_mean)
print(embarked_mode)

------------------------------------------------------------
29.69911764705882
0    S
Name: Embarked, dtype: object
```

```python
data['Age'].fillna(value=round(age_mean), inplace=True)
data['Embarked'].fillna(value=embarked_mode.values[0], inplace=True)

print(data.isnull().sum())

------------------------------------------------------------
PassengerId    0
Survived       0
Pclass         0
Name           0
Sex            0
Age            0
SibSp          0
Parch          0
Ticket         0
Fare           0
Embarked       0
dtype: int64
```
<br>

## 3) dataset 만들기

```python
import tensorflow as tf


ans = data.pop('Survived')    # seperate 'Survived' column

ds = tf.data.Dataset.from_tensor_slices( (dict(data), ans) )

print(ds)

------------------------------------------------------------
<TensorSliceDataset element_spec=({'PassengerId': 
    TensorSpec(shape=(), dtype=tf.int64, name=None), 
    'Pclass': TensorSpec(shape=(), dtype=tf.int64, name=None), 
    'Name': TensorSpec(shape=(), dtype=tf.string, name=None), 
    'Sex': TensorSpec(shape=(), dtype=tf.string, name=None), 
    'Age': TensorSpec(shape=(), dtype=tf.float64, name=None), 
    'SibSp': TensorSpec(shape=(), dtype=tf.int64, name=None), 
    'Parch': TensorSpec(shape=(), dtype=tf.int64, name=None), 
    'Ticket': TensorSpec(shape=(), dtype=tf.string, name=None), 
    'Fare': TensorSpec(shape=(), dtype=tf.float64, name=None), 
    'Embarked': TensorSpec(shape=(), dtype=tf.string, name=None)}, 
    TensorSpec(shape=(), dtype=tf.int64, name=None))>
```
+ `tf.data.Dataset.from_tensor_slices(X데이터, 정답)`
    + 지금까지는 X데이터로 `리스트/행렬/텐서`를 넣었었는데 csv 데이터처럼 column이 많은 데이터의 경우 `딕셔너리` 형태로 X데이터를 만들고 뉴럴네트워크에 넣으면 편하다. <br>
    Example)
    ```python
    data = { names: (Kim, Park, Lee, ...), ages: (20, 30, 40, ...), location: (Seoul, London, NY, ...)}
    ```
    + 이 코드로 아주 쉽게 딕셔너리 형태의 Tensorflow 데이터셋을 만들 수 있다.
    + 딕셔너리로 만드는 이유: csv 데이터를 모델에 넣을 때 feature columns 이용하면 편리!

<br>

dataset이 잘 만들어졌는지 data 1개 출력해보자.
```python
for i, l in ds.take(1):
    print(i)
    print(l)

------------------------------------------------------------
{'PassengerId': <tf.Tensor: shape=(), dtype=int64, numpy=1>, 
'Pclass': <tf.Tensor: shape=(), dtype=int64, numpy=3>, 
'Name': <tf.Tensor: shape=(), dtype=string, numpy=b'Braund, Mr. Owen Harris'>, 
'Sex': <tf.Tensor: shape=(), dtype=string, numpy=b'male'>, 
'Age': <tf.Tensor: shape=(), dtype=float64, numpy=22.0>, 
'SibSp': <tf.Tensor: shape=(), dtype=int64, numpy=1>, 'Parch': <tf.Tensor: shape=(), dtype=int64, numpy=0>, 
'Ticket': <tf.Tensor: shape=(), dtype=string, numpy=b'A/5 21171'>, 
'Fare': <tf.Tensor: shape=(), dtype=float64, numpy=7.25>, 
'Embarked': <tf.Tensor: shape=(), dtype=string, numpy=b'S'>}
tf.Tensor(0, shape=(), dtype=int64)
```

<br>

## 4) Feature Columns

csv 데이터를 한 번에 처리하고 싶으면 feature columns를 쓰자!

```python
feature_columns = []

# 숫자 그대로: Fare, SibSp, Parch
feature_columns.append(tf.feature_column.numeric_column('Fare'))
feature_columns.append(tf.feature_column.numeric_column('SibSp'))
feature_columns.append(tf.feature_column.numeric_column('Parch'))

# 숫자를 범위로 묶어서 카테고리화: Age
Age = tf.feature_column.numeric_column('Age')
Age_bucket = tf.feature_column.bucketized_column(Age, boundaries=[10,20,30,40,50,60,70])
feature_columns.append(Age_bucket)

# 카테고리 원핫인코딩: Sex, Embarked, Pclass
cat_columns = ['Sex', 'Embarked', 'Pclass']

for i in cat_columns:
    vocab = data[i].unique()
    cat = tf.feature_column.categorical_column_with_vocabulary_list(i, vocab)
    one_hot = tf.feature_column.indicator_column(cat)
    feature_columns.append(one_hot)

# 많은 카테고리 embedding: Ticket
vocab = data['Ticket'].unique()
cat = tf.feature_column.categorical_column_with_vocabulary_list('Ticket', vocab)
one_hot = tf.feature_column.embedding_column(cat, dimension=10)
feature_columns.append(one_hot)

print(feature_columns)

------------------------------------------------------------
[NumericColumn(key='Fare', shape=(1,), default_value=None, dtype=tf.float32, normalizer_fn=None), 
NumericColumn(key='SibSp', shape=(1,), default_value=None, dtype=tf.float32, normalizer_fn=None), 
NumericColumn(key='Parch', shape=(1,), default_value=None, dtype=tf.float32, normalizer_fn=None), 
BucketizedColumn(source_column=NumericColumn(key='Age', shape=(1,), default_value=None, dtype=tf.float32, normalizer_fn=None), boundaries=(10, 20, 30, 40, 50, 60, 70)), 
IndicatorColumn(categorical_column=VocabularyListCategoricalColumn(key='Sex', vocabulary_list=('male', 'female'), dtype=tf.string, default_value=-1, num_oov_buckets=0)), 
IndicatorColumn(categorical_column=VocabularyListCategoricalColumn(key='Embarked', vocabulary_list=('S', 'C', 'Q'), dtype=tf.string, default_value=-1, num_oov_buckets=0)), 
IndicatorColumn(categorical_column=VocabularyListCategoricalColumn(key='Pclass', vocabulary_list=(3, 1, 2), dtype=tf.int64, default_value=-1, num_oov_buckets=0)), 
EmbeddingColumn(categorical_column=VocabularyListCategoricalColumn(key='Ticket', vocabulary_list=('A/5 21171', 'PC 17599', 'STON/O2. 3101282', '113803', '373450', '330877', '17463', ...), dtype=tf.string, default_value=-1, num_oov_buckets=0), dimension=10, combiner='mean', initializer=<tensorflow.python.ops.init_ops.TruncatedNormal object at 0x28c776980>, ckpt_to_load_from=None, tensor_name_in_ckpt=None, max_norm=None, trainable=True, use_safe_embedding_lookup=True)]
```

먼저 각 column을 어떻게 전처리해서 `feature columns=[]`에 넣어줄지 생각한다.
+ `tf.keras.layers.DenseFeatures(feature_columns)`
    + Feature columns를 활용할 때에는 모델에 첫 레이어로 반드시 이 레이어를 써준다.
+ `feature_columns=['Age'는 이렇게 전처리, 'Fare'는 이렇게 전처리, ...]`
    + 각 column 별로 어떻게 전처리할지 명시해준다.
        + 숫자 그대로 넣기 ⇒ `tf.feature_column.numeric_column(column_name)`
        + 숫자를 범위로 묶어서 카테고리화해서 넣기 ⇒ `tf.feature_column.bucketized_column(column_name)`
        + 카테고리의 경우 원핫인코딩하기 ⇒ `tf.feature_column.indicator_column(column_name)`
        + 카테고리가 매우 많은 경우 embedding 행렬 만들어주기 ⇒ `tf.feature_column.embedding_column(column_name)`
+ `tf.feature_column.bucketized_column(Age, boundaries=[10,20,30,40,50,60,70])`
    + `Age` 컬럼의 경우 10대, 20대, 30대, ... 70대까지 나누어주었다. (describe() 통계를 확인해보면 나이가 0~80세로 나타난다.)
+ `one_hot = tf.feature_column.embedding_column(cat, dimension=10)`
    + `embedding column`: 원핫인코딩이 아니라 내가 원하는 행렬로 바꿀 수 있다. 행렬 안에 무작위의 숫자로 설정하고, 학습을 진행할수록 적절한 숫자로 바뀐다. 하나의 카테고리를 하나의 행렬로 바꿔준다.
    + `dimension`은 내가 설정하고 싶은 행렬의 차원을 입력해준다.
+ 원본데이터에서 `PassengerId`와 `Name`은 필요한 정보가 아니라 생각되어 feature columns에 포함시키지 않았다. 이렇게 feature_columns에 없으면 뉴럴네트워크가 알아서 해당 열은 포함시키지 않고 학습을 진행한다.

<br>

# 2. Model & Train

```python
model = tf.keras.Sequential([
    tf.keras.layers.DenseFeatures(feature_columns),
    tf.keras.layers.Dense(128, activation='relu'),
    tf.keras.layers.Dense(64, activation='relu'),
    tf.keras.layers.Dropout(0.2),
    tf.keras.layers.Dense(1, activation='sigmoid')
])

model.compile(optimizer='adam', loss='binary_crossentropy', metrics=['acc'])

model.fit(ds, shuffle=True, epochs=30)
```

+ 처음에 위와 같이 모델을 빌딩하고 학습을 시켜줬더니 바로 에러가 떴다. 이유는 feature column용 모델에는 `model.fit()`에 데이터셋을 넣을 때 `batch` 형태로 넣어야 하는 것이었다. 만약 batch로 넣지 않는다면 dataset shape 에러가 뜬다.
+ 데이터셋을 batch로 나눠주는 코드는 `dataset.batch(N)`

```python
ds_batch = ds.batch(32)

print(ds_batch)

------------------------------------------------------------
<BatchDataset element_spec=({
    'PassengerId': TensorSpec(shape=(None,), dtype=tf.int64, name=None), 
    'Pclass': TensorSpec(shape=(None,), dtype=tf.int64, name=None), 
    'Name': TensorSpec(shape=(None,), dtype=tf.string, name=None), 
    'Sex': TensorSpec(shape=(None,), dtype=tf.string, name=None), 
    'Age': TensorSpec(shape=(None,), dtype=tf.float64, name=None), 
    'SibSp': TensorSpec(shape=(None,), dtype=tf.int64, name=None), 
    'Parch': TensorSpec(shape=(None,), dtype=tf.int64, name=None), 
    'Ticket': TensorSpec(shape=(None,), dtype=tf.string, name=None), 
    'Fare': TensorSpec(shape=(None,), dtype=tf.float64, name=None), 
    'Embarked': TensorSpec(shape=(None,), dtype=tf.string, name=None)}, 
    TensorSpec(shape=(None,), dtype=tf.int64, name=None))>
```

<br>

batch로 나뉜 데이터셋이 `DenseFeatures layer`에 들어갈 때 어떤 모습으로 들어갈까? <br>
`Fare` column의 경우로 확인해보자.

```python
print(next(iter(ds_batch)))    # batch 데이터 중 1개

------------------------------------------------------------
({'PassengerId': <tf.Tensor: shape=(32,), dtype=int64, numpy=
array([ 1,  2,  3,  4,  5,  6,  7,  8,  9, 10, 11, 12, 13, 14, 15, 16, 17,
       18, 19, 20, 21, 22, 23, 24, 25, 26, 27, 28, 29, 30, 31, 32])>, 
'Pclass': <tf.Tensor: shape=(32,), dtype=int64, numpy=
array([3, 1, 3, 1, 3, 3, 1, 3, 3, 2, 3, 1, 3, 3, 3, 2, 3, 2, 3, 3, 2, 2,
       3, 1, 3, 3, 3, 1, 3, 3, 1, 1])>,

...,

'Fare': <tf.Tensor: shape=(32,), dtype=float64, numpy=
array([  7.25  ,  71.2833,   7.925 ,  53.1   ,   8.05  ,   8.4583,
        51.8625,  21.075 ,  11.1333,  30.0708,  16.7   ,  26.55  ,
         8.05  ,  31.275 ,   7.8542,  16.    ,  29.125 ,  13.    ,
        18.    ,   7.225 ,  26.    ,  13.    ,   8.0292,  35.5   ,
        21.075 ,  31.3875,   7.225 , 263.    ,   7.8792,   7.8958,
        27.7208, 146.5208])>,
'Embarked': <tf.Tensor: shape=(32,), dtype=string, numpy=
array([b'S', b'C', b'S', b'S', b'S', b'Q', b'S', b'S', b'S', b'C', b'S',
       b'S', b'S', b'S', b'S', b'S', b'Q', b'S', b'S', b'C', b'S', b'S',
       b'Q', b'S', b'S', b'S', b'C', b'S', b'Q', b'S', b'C', b'C'],
      dtype=object)>}, 
<tf.Tensor: shape=(32,), dtype=int64, numpy=
array([0, 1, 1, 1, 0, 0, 0, 0, 1, 1, 1, 1, 0, 0, 0, 1, 0, 1, 0, 1, 0, 1,
       1, 1, 0, 1, 0, 0, 1, 0, 0, 1])>)
```

```python
feature_layer = tf.keras.layers.DenseFeatures(tf.feature_column.numeric_column('Fare'))

print(feature_layer(next(iter(ds_batch))[0]))

------------------------------------------------------------
<tf.Tensor: shape=(32, 1), dtype=float32, numpy=
array([[  7.25  ],
       [ 71.2833],
       [  7.925 ],
        ...,
       [  7.8958],
       [ 27.7208],
       [146.5208]], dtype=float32)>
```
+ 위 결과와 같이 전처리된 형태로 레이어에 들어가는 것 확인!

<br>

이제 다시 모델에 batch 데이터셋을 넣어 학습시켜보자!

```python
model.fit(ds_batch, shuffle=True, epochs=30)

--------------------------------------------------------------------------------
Epoch 1/30
28/28 [==============================] - 1s 20ms/step - loss: 0.9344 - acc: 0.5578
Epoch 2/30
28/28 [==============================] - 0s 16ms/step - loss: 0.6291 - acc: 0.6958
Epoch 3/30
28/28 [==============================] - 1s 19ms/step - loss: 0.5509 - acc: 0.7262
...
Epoch 29/30
28/28 [==============================] - 0s 16ms/step - loss: 0.0267 - acc: 0.9899
Epoch 30/30
28/28 [==============================] - 0s 16ms/step - loss: 0.0333 - acc: 0.9888
```

모델이 잘 돌아가고 정확도도 98%로 높은 성능을 보였다.

+ (참고) 만약 validation dataset을 만들어 validation accuracy를 확인하고 싶다면 batch로 데이터셋을 나누기 전에 원본 데이터를 먼저 train dataset, validation dataset으로 나누어 진행하면 될듯하다. (batch로 나눈 데이터셋으로 바로 `model.fit(ds_batch, validation_split=0.2`를 해주면 에러가 뜬다. batch로 나눈 데이터셋은 validation split이 불가능하기 때문)
+ 타이타닉 생존자 예측은 마치 환자와 환자 관련 데이터를 통해 환자의 질병 여부를 예측하는 로직이랑 비슷하게 생각할 수 있다. 본 프로젝트를 응용하여 환자를 예측하는 medical AI 분야에 적용할 수 있을 것이다.

<br>

# Reference
+ [코딩애플 - 문과여도 상관없다 Tensorflow 딥러닝 AI 기초부터 실무까지](https://codingapple.com/course/python-deep-learning/)
