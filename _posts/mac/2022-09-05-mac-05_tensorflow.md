---
title: "[MAC] M1 MacBook Pro Environment setting - #5. Tensorflow"
description: "Installing Tensorflow on M1 Mac for Machine Learning"
author: aijinsol
date: 2022-09-05
categories:
  - MAC
tags:
  - tensorflow
---

# 1. Conda를 이용한 가상환경 구축

**Step 1)** Tensorflow 가상환경을 구축하기 위한 폴더 생성
    
```bash
mkdir {FOLDER_NAME}
cd {FOLDER_NAME}
```
    
**Step 2)** Conda 가상환경 구축 및 활성화
    
```bash
conda create -n {ENV_NAME}
```

![fig1](/statics/posts/mac/mac-05-fig1.png){: width="550"}
    
<br>

# 2. Tensorflow depedencies 설치

Apple에서 M1 MacBook에서 Tensorflow를 사용 가능하도록 만든 Tensorflow dependencies를 Apple conda channel를 통해 설치할 수 있도록 제공한다.

```bash
# Tensorflow를 설치할 가상환경 내부에서 진행
conda install -c apple tensorflow-deps
```

![fig2](/statics/posts/mac/mac-05-fig1.png){: width="550"}

<br>

# 3. Tensorflow-macos 설치

MacOS를 위한 Tensorflow 설치

```bash
python -m pip install tensorflow-macos
```

![fig3](/statics/posts/mac/mac-04-fig3.png){: width="550"}

<br>

# 4. Tensorflow-metal 설치

Apple의 `tensorflow-metal` 은 M1, M1 Pro, M1 Max에서 GPU acceleration을 가능하게 해준다. ([Metal](https://developer.apple.com/metal/)은 Apple의 GPU framework)

```bash
python -m pip install tensorflow-metal
```

![fig4](/statics/posts/mac/mac-04-fig4.png){: width="550"}

<br>

# 5. (optional) Tensorflow datasets 설치

```bash
python -m pip install tensorflow-datasets
```

![fig5](/statics/posts/mac/mac-04-fig5.png){: width="550"}

<br>

# 6. (optional) Data science 패키지 설치

```bash
conda install jupyter pandas numpy matplotlib scikit-learn
```

<br>

# 7. Dependencies & GPU acceleration 확인

처음 import 할 때 약간의 시간이 걸린다 (약 1분 정도)

```python
import numpy as np
import pandas as pd
import sklearn
import tensorflow as tf
import matplotlib.pyplot as plt

# Check for TensorFlow GPU access
print(f"TensorFlow has access to the following devices:\n{tf.config.list_physical_devices()}")

# See TensorFlow version
print(f"TensorFlow version: {tf.__version__}")
```

![fig6](/statics/posts/mac/mac-04-fig6.png){: width="550"}

위의 코드 실행 결과, 앞으로 Tensorflow를 사용할 때 CPU 뿐만 아니라 GPU도 함께 accelerate된다는 것을 확인했다.

<br>

# Reference

[Setup Apple Silicon Mac for Machine Learning in 13 minutes (TensorFlow edition)](https://www.youtube.com/watch?v=_1CaUOHhI6U)

[Tensorflow on Apple Silicon](https://github.com/mrdbourke/m1-machine-learning-test)
