---
layout: post
title: PIL vs OpenCV
tags: [Python]
---

Image 관련 task에서, Pytorch를 이용해 딥러닝 모델 학습을 위한 이미지를 불러올 때, 보통 OpenCV와 PIL 두 라이브러리 중 하나를 이용해 이미지를 불러옵니다.

그런데 torchvision.transforms 모듈에서 지원하는게 PIL의 Image array여서 일반적으론 PIL을 많이 사용합니다. 

그렇다면 두 라이브러리의 성능은 어떨까요? 결과는 다음과 같습니다.

<br>

## PIL vs OpenCV

**OpenCV**

- BGR로 이미지 읽어옴
- Video capture와 같은 기능 지원이 잘 되어있음
- PIL에는 없는 다양한 함수들 지원(PIL에 있는 거의 모든 기능이 OpenCV에 존재)
- cv2 함수의 입력으로 numpy array를 넣어서 바로 사용가능(numpy array와의 호환성이 좋음)
- C++에서 OpenCV를 배우고 사용시 익숙해지기 쉬움(반대도 비슷함)
- numpy array 인덱싱을 이용해 이미지에 대한 전처리가 가능하기에 좀 더 자유롭게 이미지 전처리 가능
- torchvision과의 호환성이 안좋음, 그렇지만 opencv와 호환되는 torchvision [버전도 존재](https://github.com/jbohnslav/opencv_transforms). (오피셜 코드는 아닙니다)

**PIL**

- RGB로 이미지 읽어옴
- torchvision에서의 지원이 잘 되어있음
- numpy array와의 암묵적 casting이 안되고, numpy array를 Image array로 바꿔줘야함(은근 귀찮음)
- numpy array 인덱싱을 이용해 이미지에 대한 전처리를 하던 사람은 사용시 불편함을 느낄 수 있음

<br>

**Comparison**

[Pillow performence](https://python-pillow.org/pillow-perf/) benchmark에서 다양한 조건하에서 여러 라이브러리의 성능을 비교했습니다.

결론을 말씀드리면 PIL 라이브러리의 경우는 OpenCV보다 전체적인 성능이 떨어집니다. 하지만 PIL로 부터 fork된 Pillow-SIMD 라이브러리의 경우 OpenCV와 대등한 성능을 내거나 더 좋은 성능을 내는 항목이 많았습니다.

(PIL->Pillow->Pillow-SIMD순으로 fork 되었습니다.)

개인적으로는 C++에서 OpenCV를 사용하다가 넘어와서 그런지 PIL이 친숙하지 않고, 전처리하는 방식이 덜 직관적이었습니다. 또 OpenCV에서 지원하는 강력한 함수들(이미지의 binarization, 전통적인 Computer Vision 알고리즘)이 지원이 되지 않아 아쉬운 느낌이 많았습니다.



<br>

**참고자료**

[Kaggle](https://www.kaggle.com/vfdev5/pil-vs-opencv)

[Quora](https://www.quora.com/Whats-the-difference-between-following-python-packages-CV2-PIL-and-OPENCV-When-can-I-use-each-of-them)

<br>