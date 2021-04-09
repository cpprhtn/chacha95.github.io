---
layout: post
title: Object detection dataset 리뷰
tags: [Object Detection]
use_math: true
---

# Introudction

Object detection에 쓰이는 데이터셋은 정말 다양하게 존재합니다. 최근에는 Google, Facebook, Microsoft와 같은 기업들이 대규모 데이터셋들을 공개하고 있고, 공개되고 있는 데이터셋의 난이도가 올라가고 있습니다. 그 중 중요하고 자주 쓰이는 것들에 대해 포스트 해보겠습니다.

<br>

## PASCAL VOC

PASCAL VOC는 PASCAL VOC challenge에서 쓰이던 데이터셋입니다. 2005년에서 2012년까지 진행되었으며, 그 중 PASCAL 2007과 PASCAL 2012 데이터셋이 벤치마크 데이터셋으로 자주 쓰입니다. 

> VOC 데이터셋

![](https://user-images.githubusercontent.com/31475037/75751537-121c9100-5d6a-11ea-824a-de87ce09bfe7.png)

PASCAL VOC 2007년 challenge 이전에는 object detection 결과 평가에 대해 다양한 방식으로 평가 했으나, 이후 AP(Average Precision)을 기반으로 평가하는 방식이 보편화 됬습니다. 현재로선 AP를 이용한 평가 방식이 **de facto**(업계 표준)가  됬습니다.

### 특징

VOC challenge는 다음과 같은 특징이 있습니다.

- 이미지 개수: 11k
- 이미지당 평균 object 수: 2.4개
- IoU threshold: 0.5

현재는 벤치마크용으로만 사용되며, 학습용으론 잘 쓰이지 않는 데이터셋입니다.



### Task

VOC challenge에는 다음과 같은 task가 있습니다.

- Classification
- Object detection
- Semantic segmentation
- Action detection

<br>

## MS COCO

COCO 데이터셋은 ImageNet 데이터셋의 문제점을 해결하기 위해 2014년 제안되었습니다.

ImageNet 데이터셋은 다음과 같은 문제점을 지니고 있습니다.

- 이미지 내 object가 큰 편임
- object가 중앙에 잘 위치해 있음
- 이미지당 object 수가 적음

> ImageNet 데이터셋

![](https://thegradient.pub/content/images/2018/07/image_1.png)

위의 문제점 때문에 PASCAL VOC나 ImageNet이 현실세계 사진에서는 object를 잘 포착하지 못한다는 문제점이 있었습니다.



### 특징

이러한 문제를 해결한 것이 MS COCO 데이터셋이고 다음과 같은 특징을 지닙니다.

- 다양한 크기의 물체가 존재하며, 높은 비율로 작은 물체들이 존재

  ![](https://user-images.githubusercontent.com/31475037/75751551-1779db80-5d6a-11ea-872f-514bdbf232b7.png)

- 덜 iconic 합니다. 여기서 iconic의 의미는 이미지가 특정 카테고리에 명확하게 속해있는 경우를 말합니다. 예를들어 아래 이미지의 (a)의 이미지들은 누가봐도 명확하게 개, 소 등의 명확하게 특정 카테고리에 속한걸 누구나 알 수 있습니다. (b)의 이미지들은 (a) 보다는 모호하지만, 특정 카테고리에 속하다고 말할 수 있습니다. 그런데 (c)의 경우 어떤 카테고리에 속하는지 분류하기가 모호합니다. 현실세계 사진에서는 (a)나 (b)보단 (c)에 속하는 경우가 훨씬 많이 존재합니다.

  ![](https://user-images.githubusercontent.com/31475037/75751555-18ab0880-5d6a-11ea-90e0-55d9cfd625e4.png)

- Object들이 혼잡하게 존재하고, occlusion(폐색)이 많이 존재합니다.

  > 실제 COCO 예시

  ![](https://user-images.githubusercontent.com/31475037/75751558-19dc3580-5d6a-11ea-988c-fd0ee56633e8.png)

- Thing과 stuff를 구분해 thing만 레이블링 했습니다. 여기서 thing은 쉽게 레이블링 될 수 있는 물체를 의미합니다. Stuff는 쉽게 레이블링 될 수 없는 물체를 의미합니다.

  예를 들어 사람, 차, 개는 쉽게 레이블링이 되어 thing이지만, 잔디, 하늘등은 쉽게 레이블링되지 않아 stuff입니다.

  > Thing과 stuff의 개념 제시

  ![](https://user-images.githubusercontent.com/31475037/75751560-1a74cc00-5d6a-11ea-9ad3-cb7cfd916ccf.png)

- 이미지 개수: 320k

- 이미지 당 object 수: 8개



### Task

COCO 데이터셋은 정말 다양한 task를 위해 만들어 졌으며 대표적인 task는 다음과 같습니다.

- classification
- object detection
- semantic segmentation
- instance segmentation
- pose estimation

> MS COCO 데이터셋

![](https://user-images.githubusercontent.com/31475037/75751546-15b01800-5d6a-11ea-887f-9cd9607553e4.png)



### COCO 생성 방식

MS COCO는 AMT(Amazone Mechanical Turk)라는 웹 기반 데이터 크라우드 소싱 방식을 이용해 저렴한 비용으로 데이터셋을 제작하였습니다. AMT를 통해 고용된 노동자들이 데이터셋을 레이블링한 결과물을 expert들이 투표를 통해 결과에 대해 평가하거나, expert들이 만들어낸 결과물과의 IoU를 비교해 결과 평가를 하였습니다.



### COCO 평가 방식

COCO 이전에는 PASCAL VOC 2007에서 제안한 AP를 각 클래스 별로 구한뒤, mAP(mean Average Precision)을 구하는 방식을 많이 사용했는데요, COCO에선 mAP 계산시 IoU를 유동적으로 사용하거나 AR(Average Recall)을 이용해 평가하는 방식을 사용합니다.

> 평가 방식

![](https://user-images.githubusercontent.com/31475037/75751561-1ba5f900-5d6a-11ea-8a0a-7c1370c2a405.png)



기본적으로 IoU의 threshold를 0.5로 두고 평가를 하는데, COCO에서는 0.5 뿐만 아니라 0.75 및 0.5부터 0.95까지 0.5씩 늘려가면서 다양한 IoU threshold에 대해 측정하는 방식을 기본으로 내세웁니다. 이러한 측정 방식을 낸 이유는 아마 이미지내 많은 수의 작은 객체가 있을 때는 IoU가 높아야 제대로 예측했다고 볼 수 있기 때문일 것이라 추측됩니다.

> COCO에서 기본이 되는 metric

![](https://user-images.githubusercontent.com/31475037/75751564-1cd72600-5d6a-11ea-8b59-16bc662f584f.png)



이 외에도 객체의 크기에 따라 측정하는 방식도 제안합니다. COCO 데이터셋에선 큰 물체보다 작은 물체가 더 많습니다. 대략 41%의 물체가 small(면적 <32^2)이고 34%가 medium(32^2 <면적 <96^2)이고 24%가 large(면적> 96^2)라고 분류됩니다. 면적은 분할 마스크의 픽셀 수로 측정됩니다. 

또한 기존에 AP를 이용하는 방식 말고도 AR을 기준으로 평가하는 방식을 도입함으로써 다양한 측면에서 모델을 평가하고 있습니다.

<br>

**참고 강의**

[towards data science](https://towardsdatascience.com/evaluating-performance-of-an-object-detection-model-137a349c517b)

[COCO dataset](http://cocodataset.org/#detection-eval)