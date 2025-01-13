---
title: "[ML] Dataset Split"
description: ""
author: aijinsol
date: 2022-03-10
categories:
 - ML
tags:
  - scikit-learn
---

# 일정한 class 비율로 dataset split 하기

> 머신머닝 모델을 돌릴 때 train dataset에 overfitting되지 않도록 하기 위해 class의 비율이 일정한 train과 validation(test) dataset으로의 split이 중요하다.

+ `scikit-learn` 라이브러리의 `model_selection` 패키지에서 `train_test_split` 함수와 `stratify` 파라미터를 활용하면 original dataset에서의 target data(class)의 비율과 동일한 train & validation(test) dataset으로 split이 가능하다.

```python
from sklearn.model_selection import train_test_split


x_train, x_test, y_train, y_test = train_test_split(x_data, y_data, shuffle=True, stratify=y_data, test_size=0.3, random_state=0)
```

+ Option 값

    + `shuffle` : default=True. Split하기 전 data를 섞어줄지 결정하는 옵션.

    + `stratify` : defalut=None. `stratify` 값을 target값으로 지정하면 전체 dataset에서의 class 비율이 train & validation(test) dataset에 유지된다. (Classification 모델 성능에 매우 중요한 사항!)

    + `test_size` : default=0.25. Test dataset의 구성비율 결정 옵션.

    + `random_state` : 같은 random seed를 사용한다면 같은 dataset이 추출된다. 여러 작업자가 공동작업할 때 유용하다.
