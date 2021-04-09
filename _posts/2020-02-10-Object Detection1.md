---
layout: post
title: Object Detection(객체 검출) 1
tags: [Object Detection]
use_math: true
---

# Object Detection(객체 검출) 이란?

Object detection(객체 검출)은 입력 영상이 주어질 때, 영상 내에 존재하는 모든 카테고리에 대해서 classification(분류)과 localization(지역화)를 수행하는 task입니다.

Localization은 bounding box를 찾는 regression(회귀)이고, classification은  bounding box 내 물체가 무엇인지 분류하는 문제입니다.

> object detection = classification + localization

![](https://user-images.githubusercontent.com/31475037/74116000-ef78db80-4bf4-11ea-8b7a-ba42fed2db57.png)

입력 영상에 따라 존재하는 물체의 개수가 일정치 않고, 0~N개로 변하기에 난이도가 높은 문제입니다.

Object detection과 유사한 문제로는 semantic segmentation 문제가 존재합니다. semantic segmentation은 각 픽셀에 대해 클래스를 정해주는 문제입니다. 각 픽셀마다 클래스를 예측하기에 semantic segmentation은 dense prediction이라고도 불립니다.

Semantic segmentation은 같은 클래스의 instance(물체)를 구별하지 않지만, instance segmentation의 경우 같은 class의 다른 instance(물체)를 구분합니다.

> 각 task별 차이점

![](https://user-images.githubusercontent.com/31475037/74116002-f0117200-4bf4-11ea-81e2-a09a25ab6e91.png)

<br>

## Measurement(평가)

Object detection에서 dataset은 non-uniform 하기에 기존에 classification에서 사용하던 accuracy 지표는 거의 사용하지 않습니다. 대신 Confusion matrix 기반의 AP지표를 주로 사용합니다.

Object detction에 대한 평가 방법은 다음과 같습니다.

### IoU(Intersection over Unit)

Object detection에서 bounding box를 얼마나 잘 예측하였는지는 IoU라는 지표를 통해 측정합니다. 정답인 GT(Ground Truth)와 예측된 bounding box가 얼마나 겹치는가를 측정합니다.

> IoU 계산 예시

![](https://hoya012.github.io/assets/img/object_detection_fourth/fig1.PNG)



IoU가 특정 값(threshold)을 넘으면 정답으로 취급됩니다.

- PASCAL VOC: 0.5
- MS COCO: 0.5, 0.55, 0.6 ..... 0.95



> IoU 값 예시

![](https://www.pyimagesearch.com/wp-content/uploads/2016/09/iou_examples.png)



### Confusion matrix(혼동행렬)

Confusion matrix NxN으로 이루어진 행렬입니다. 모델 성능 평가에 이용됩니다. 아래 그림은 임신 판별에 대한 confusion matrix 예시입니다.

> Confusion Matrix의 예시

![](https://miro.medium.com/max/462/1*7EYylA6XlXSGBCF77j_rOA.png)

- **True Positive(TP)**

  positive로 예측을 했고, 그것이 맞았습니다.

- **True Negative(TN)**

  negative로 예측을 했지만, 그것이 맞았습니다.

- **False Positive(FP)**

  positive로 예측을 했지만, 그것이 틀렸습니다.

- **False Negative(FN)**

  negative로 예측을 했지만, 그것이 틀렸습니다.

예시로 임신한 경우(positive) 또는 임신하지 않은 경우(negative) 샘플 100개의 **Recall**, **Precision**을 계산해 보겠습니다.

> 임신 검사에 대한 Confusion Matrix

![](https://user-images.githubusercontent.com/31475037/75405887-4d762480-5952-11ea-99c8-e6c013654ca3.png)

### Recall

- 한국말로는 재현율(직관적으론 검출율이라고 봐도 됨)
- 검출되야하는 물체들 중에서 제대로 검출된 것의 비율
- 대상 물체들을 빠뜨리지 않고 얼마나 잘 잡아내는지를 나타냄
- 실제 positive 중 정확히 positive라고 식별된 사례의 비율

> 재현율 계산

![](https://miro.medium.com/max/246/1*BT3awaBdZHsit5s41LPb9A.png)



### Precision

- 한국말로는 정밀도
- 모든 검출 결과 중 옮게 검출한 비율
- positive로 식별된 사례 중 실제 positive 사례의 비율

> 정밀도 계산

![](https://miro.medium.com/max/249/1*QRIZDkk_FffXKs_07ZlhZw.png)

### Precision과 Recall의 관계

precision과 recall은 반비례하는 경향성을 가집니다. Recall=100%를 달성하기 위해선 모든 입력에 대해 객체가 검출되도록 알고리즘을 설계하면됩니다. 이런 경우 당연히 precision이 떨어질 수 밖에 없습니다. 반대로 precision=100%를 달성하기 위해 객체 검출의 기준을 높여버리면, recall이 떨어지게 됩니다.

> 모든 입력에 대해 객체가 검출되도록하면 아래와 같이 아무도 없는 공간에서 사람이 검출될 수도 있다.(귀신일수도?)

<center><img src="https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=http%3A%2F%2Fcfile10.uf.tistory.com%2Fimage%2F9982BB3A5CDE694C0E9DF1" width="70%"></center>
이런 반비례 상황에서, 알고리즘을 제대로 평가하기 위해선 precision-recall 그래프를 활용해야 합니다. 먼저 Recall을 0.1 단위 증가 시켰을 때, 각 단위별 precision의 값을 구하면 precision-recall 그래프가 나오게 됩니다.

> recall을 0.1 단위로 증가시켜 precision 값을 구함

<center><img src="https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fk.kakaocdn.net%2Fdn%2FqsCQt%2FbtqufbT4BWO%2FRgG7ha2HvpWv52sp4k4sEk%2Fimg.bmp"></center>
이후 그래프를 살짝 변형시켜 사각형의 형태로 만들고, 해당 사각형의 넓이를 구하면 그게 **Average Precision(AP)**입니다.

> 빨간색 사각형의 영역이 AP이다.

![](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fk.kakaocdn.net%2Fdn%2Fbg73Gf%2FbtquhjDtzFh%2FETRORigF8P4dT02zB4kox1%2Fimg.png)

Object detection 문제에서 object 분류시 class가 여러개면 각 클래스당 AP를 구한 다음, 그것을 모두 합한뒤 물체 클래스의 갯수로 나눠 평균값을 구하는 지표를 사용합니다. 해당 지표가 바로 mean Average Precision(mAP)입니다. 

## Object Detection에선 왜 accuracy를 잘 사용하지 않나요?

Object detection에서 평가 지표로 mAP가 처음 사용된게 VOC2007에서 였습니다. VOC 2007 데이터셋을 보면 카테고리별 개수의 차이가 큰, **dataset imbalance** 문제가 존재했습니다. 대표적으론 데이터셋 내부에 사람 category가 대다수였습니다. 이러한 데이터셋의 특성 때문에, 이를 극복하기 위해 제안된게 Confusion matrix를 이용한 mAP였고, 이후 해당 방식을 표준적인 평가지표로 사용하기 시작했습니다.

<br>

## 기존 방법론

전통적인 computer vision, image processing에서의 object detection에 대해 간단히 소개드립니다. 

가장 간단히 생각해 볼 수 있는 방법은 물체가 존재할 수 있는 모든 크기의 영역에 대해 sliding window 방식으로 이미지를 모두 탐색하는 방식입니다. 사용자가 patch(영역)의 크기를 지정해 모든 영역을 탐색합니다.

그렇지만 이 방식은 탐색 영역의 수가 너무 많고, 겹치는 patch에 대해 feature(특징)를 공유하지 않아 computational cost(연산 시간, 비용)이 많이 소모됩니다.

> 가장 간단한 방법 - sliding window

![](https://user-images.githubusercontent.com/31475037/74116003-f0aa0880-4bf4-11ea-9e6c-cb92482f25cd.png)



이러한 문제점을 해결하기 위해 여러 방법론들이 제안되었고 전체 영역을 빠짐없이 탐색하는 것보다는 물체가 있을 법한 영역을 찾아내는 알고리즘들이 제안 되었습니다. 이러한 알고리즘들은 region proposal(지역 제안) 알고리즘이라 불립니다. 이 중 R-CNN과 연관이 깊은 selective search에 대해 알아 보겠습니다.

> 물체가 있을 법한 영역을 제안하는 region proposal 알고리즘

![](https://user-images.githubusercontent.com/31475037/74116005-f1429f00-4bf4-11ea-8314-d66839296806.png)



이미지 내의 구조적 특징(색상, 무늬, 크기, 모양)의 similarity(유사도)를 계산해 객체의 후보 영역을 뽑아내는 방식입니다. 각 컬러공간(RGB, HSV), 텍스쳐 등의 요소의 히스토그램을 구해서 similarity를 계산, 비슷한 것 끼리 영역을 통합시키는 bottom-up 방식입니다. 방법론적으로 보자면 low-level feature에 대해 superpixel을 greedy하게 합치는 알고리즘입니다.

> Selective search 예시

![](https://user-images.githubusercontent.com/31475037/74116006-f1db3580-4bf4-11ea-8648-ef6e01a323f3.png)



Selective search는 크게 3개의 스텝으로 구성됩니다.

1. 초기 sub-segmentation을 수행

   "Efficeint graph-based image segentation" 논문의 방식을 이용, 각각의 객체가 1개의 영역에 할당 될 수 있도록 많은 초기 영역을 생성

2. 작은 영역을 반복적으로 큰 영역으로 통합

   Greedy 알고리즘을 사용하여 작은 영역을 큰 영역으로 통합

3. 통합된 영역을 바탕으로 후보 영역 생성

여기서 similarity를 계산하는 방식은 세가지 입니다.

1. 컬러 유사도

   각각 컬러 채널에 대하여 히스토그램을 구함

2. 텍스쳐 유사도

   SIFT를 이용, 8방향의 가우시안 미분값을 구하여 히스토그램 계산

3. 크기 유사도

   영역 합칠 때 작은 영역을 우선적으로 고려하도록 만듦

> Selective search의 순서

![](https://user-images.githubusercontent.com/31475037/74121941-da0fab80-4c0c-11ea-88b0-04e7bbaab1c3.png)

이러한 selective search는 SVM과 같이 사용되어 2013년도 ILSVRC의 detection 분야에서 1등을 했으며, 굉장히 좋은 성능을 보여주었습니다.

<br>

## Stage에 따른 분류

Deeplearning 이전의 방법론들은 딱히 stage에 따른 구분을 하지 않았지만, deeplearning 이후의 기술들은 stage에 따른 구분을 크게 2가지로 두고 있습니다. 2-stage detector는 region proposal을 하는 부분과 classificiation을 하는 부분이 두단계로 명확히 나누어져 있는 구조이고, 1-stage detector는 region proposal과 classification이 동시에 이루어지는 구조 입니다. 

> 2-stage detector

![](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fk.kakaocdn.net%2Fdn%2FbwAjv7%2FbtqtUkDqdlo%2FnggGklm6kdXb5g86FYFESK%2Fimg.png)



> 1-stage detector

![](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fk.kakaocdn.net%2Fdn%2Fy6skO%2FbtqtWg0UzeE%2FhhnzQ86r8Ef1mT9Xc7GUzK%2Fimg.png)



이렇듯 object detection은 stage 별로 크게 두가지 흐름으로 발전하고 있는 상황입니다.

> object detection 발전 흐름도

![](https://user-images.githubusercontent.com/31475037/75324222-d2612f80-58b9-11ea-8ae5-e1ad0e1ccfd5.png)



다음 포스트에는 1-stage와  2-stage 각각 종류별로 리뷰를 할 예정입니다.

<br>

**참고 강의**

CS231N

[hoya012 blog](https://hoya012.github.io/blog/Tutorials-of-Object-Detection-Using-Deep-Learning-how-to-measure-performance-of-object-detection/)

[Google 머신러닝](https://developers.google.com/machine-learning/crash-course/classification/precision-and-recall?hl=ko)

[Precision and Recall에 대한 해석](https://darkpgmr.tistory.com/162)

[Lunit Blog](https://blog.lunit.io/2017/06/01/r-cnns-tutorial/)

[Guide of Faster R-CNN based on Pytorch Code](https://medium.com/@fractaldle/guide-to-build-faster-rcnn-in-pytorch-95b10c273439)

[Object detection review](https://arxiv.org/pdf/1905.05055.pdf)

[Confusion Matrix](https://towardsdatascience.com/understanding-confusion-matrix-a9ad42dcfd62)