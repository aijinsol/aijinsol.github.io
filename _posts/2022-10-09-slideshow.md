---
title: "[OpenCV] 이미지 슬라이드쇼 만들기"
excerpt: ""
categories:
 - ComputerVision
tags:
last_modified_at: 2022-10-09
---

> `OpenCV`를 이용해 이미지 슬라이드쇼 프로그램을 만들어보자.

```python
import sys
import glob
import cv2


# image files to a list
img_files = glob.glob('./images/*.jpg')

if not img_files:
    print('No image files.')
    sys.exit()

# make full window screen
cv2.namedWindow('image', cv2.WINDOW_NORMAL)
cv2.setWindowProperty('image', cv2.WND_PROP_FULLSCREEN, cv2.WINDOW_FULLSCREEN)

# show images
cnt = len(img_files)
idx = 0

while True:
    img = cv2.imread(img_files[idx])

    if img is None:
        print('Image load failed!')
        break

    cv2.imshow('image', img)
    if cv2.waitKey(2000) >= 0: # wait for 2sec
        break

    idx += 1
    if idx >= cnt:
        idx = 0     # go back to the first image

cv2.destroyAllWindows()
```