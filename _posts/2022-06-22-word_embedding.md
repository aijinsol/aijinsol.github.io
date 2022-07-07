---
title: "[NLP] Word Embedding"
excerpt: "sparse representation, dense representation, word embedding"
categories:
  - NLP
tags:
  - sparse representation
  - dense representation
  - one-hot encoding
  - word2vec
  - word embedding
last_modified_at: 2022-06-28
---

> 컴퓨터는 자연어를 이해할 수 없다. 따라서 기계가 이해할 수 있도록 수치화해주는 작업이 필요하다. `word embedding`은 컴퓨터가 텍스트를 이해할 수 있도록 텍스트를 숫자로 변환하는 방법이다. 각 단어를 인공신경망 학습을 통해 벡터화하게 된다. <br>
> 단어를 벡터화하는 방법에는 `sparse representation`와 `dense representation` 변환법이 있다. word embedding의 경우에는 dense representation으로 변환한다.

# 1. Sparse Representation (희소 표현)

> `One-hot encoding`

- 의미
    - `One-hot encoding`을 통해 나온 one-hot 벡터들은 나타내고자 하는 단어의 인덱스 값만 1이고, 다른 인덱스는 모두 0으로 표현된다. 이렇게 vector 또는 matrix의 값 대부분이 0으로 표현되는 방법을 sparse representation이라고 한다.
    - 해당 속성이 가질 수 있는 모든 경우의 수를 각각의 독립적인 차원으로 표현한다.
- 문제점
    - 단어의 개수가 증가할수록 벡터의 차원이 커지며 고차원의 벡터가 된다. 이러한 벡터 표현은 공간적 낭비를 불러일으킨다. <br>
    ex) corpus에 단어가 1,000개라면 벡터의 차원은 1,000.
    - 이러한 고차원으로 모델 학습 시 차원의 저주(Curse of Dimensionality)를 불러와 좋은 성능을 기대하기 어렵다. (입력 데이터에 0이 너무 많아 데이터에서 정보를 뽑아내기 어려워진다.)
    - 단어의 등장유무만 고려하고, 단어의 순서를 고려하지 않아 단어의 의미와 유사도를 표현하지 못한다.

<br>

# 2. Dense Representation (밀집 표현)

> `word2vec`

- 의미
    - `Dense representation`은 또 다른 말로 `Distributed representation`이라고도 불린다. `Distributed`라는 단어가 붙는 이유는 하나의 정보가 여러 차원에 분산되어 표현되기 때문이다. `Sparse representation`에서는 각각의 차원이 각각의 독립적인 정보를 갖고 있지만, `Dense representation`에서는 하나의 차원이 여러 속성들이 버무려진 정보를 갖고 있다. 즉, 하나의 차원이 하나의 속성을 명시적으로 표현하는 것이 아니라 여러 차원들이 조합되어 나타내고자 하는 속성들을 표현하게 된다.
    - 사용자가 설정한 값으로 단어의 벡터 차원을 맞추며, 따라서 벡터의 차원이 단어 집합의 크기가 되지 않는다. 즉, 각각의 속성을 독립적인 차원으로 나타내지 않고, 사용자가 정한 개수의 차원으로 대상을 대응시켜 표현한다. 예를 들어, 대상의 속성을 3차원으로 표현할 것이라 정하면 그 속성을 3차원 벡터에 대응시킨다. 이러한 대응을 `embedding`이라 한다. embedding하는 방식(즉, 단어 벡터들의 값들)은 머신러닝을 통해 학습된다. `word2vec`은 이 값들을 학습하는 방법론 중의 하나이다.
    - 벡터의 값으로는 0과 1이 아닌 실수값을 가진다.
    - corpus를 학습하여 문맥 상 비슷하게 나타나는 단어들이 비슷한 벡터들로 할당되게 된다.
- 장점
    - One-hot 벡터들보다 차원이 줄어들어 공간을 절약할 수 있다.
    - 입력 데이터의 차원이 높으면 차원의 저주(curse of dimensionality) 문제가 생기는데, dense representation에서는 입력 데이터에 0이 적어 데이터에서 정보를 뽑아내기 쉬워진다. 즉, 각각의 차원들이 모두 정보를 갖고 있어 모델이 작동하기 훨씬 쉬워진다.
    - 단어 간 의미를 표현할 수 있어 효율성이 높다.
    
![image01](/assets/images/2022-06-22-nlp_01.png){: .align-center}
*(좌) sparse representation, (우) dense representation (차원의 개수를 3으로 설정)*

<br>

# 3. Word Embedding (워드 임베딩)

- 의미
    - 사람이 사용하는 자연어의 형태를 컴퓨터가 이해하는 벡터로 변환하는 함수 또는 mapping table을 만들어내는 과정.
    - 단어를 `dense representation` 방법으로 변환하여 `dense vector` 형태로 표현하는 방법을 `word embedding`이라 한다. 또한, `word embedding`을 통해 나온 결과 벡터를 `embedding vector`라 부른다.
- 알고리즘
    - LSA, Word2Vec, FastText, GloVe 등
- 학습 방식
    - 단어를 랜덤한 값을 가지는 dense vector로 변환한 뒤, 인공신경망의 가중치를 학습
    
<br>

|  | One-hot Vector | Embedding Vector |
| :---: | :---: | :---: |
| 차원 | 고차원(단어 집합 크기) | 저차원 |
| 다른 표현 | sparse vector | dense vector |
| 표현 방법 | 수동 | 훈련 데이터로부터 학습 |
| 값의 타입 | 1, 0 | 실수 |

<br>

# Reference

- [딥러닝을 이용한 자연어 처리 입문 wikidocs](https://wikidocs.net/33520)
- [word embedding 블로그 포스팅](https://yoon1seok.tistory.com/33)
- [word2vec 블로그 포스팅](https://dreamgonfly.github.io/blog/word2vec-explained/#%EC%B0%B8%EA%B3%A0-%EC%9E%90%EB%A3%8C)