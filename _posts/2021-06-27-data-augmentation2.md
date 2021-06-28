---
layout: post
title: TTA(Test Time Augmentation) 소개
tags: [Data Augmentation]
use_math: true
---

# What is TTA?

TTA(Test Time Augmentation)이란, 모델을 test(inference)시 augmentation을 하는 방식입니다. 기존 data augmentation을 이용한 성능 향상은 모델 학습 과정 중 적용했지만, TTA는 이미 학습된 모델에 적용 가능합니다. 일종의 data ensemble이라 볼 수도 있습니다.

<br>

## TTA example

TTA의 예시는 다음과 같습니다. 원본 입력 이미지 한 장을 augmentation해 2장을 생성해냅니다. 총 3장의 이미지를 학습된 모델에 각각 입력해주고, 모델에서 나온 예측 확률 값(score)을 평균해 최종 클래스를 예측합니다.

> TTA example

![](https://user-images.githubusercontent.com/31475037/123568577-94eb1a00-d7ff-11eb-8182-14abc59ce384.png)

<br>

# TTA in Image Classification

Image Clssification의 경우 굉장히 다양한 augmentation 기법이 사용되고 있습니다. Flip, crop, scale, rotate, shift를 보통 많이 사용합니다.

> Image classication에서 TTA

![](https://user-images.githubusercontent.com/31475037/123568575-94eb1a00-d7ff-11eb-9aa6-92ca1ade1895.png)

<br>

# TTA in Object Detection

Object detection에선 HorizontalFlip과 Multisize 기법이 많이 쓰이고 있습니다. 이는 bbox나 mask의 존재 및 vertical flip과 같은 기법을 쓰게 되면 성능이 오히려 떨어지거나 미미한 성능향상에 그치기 때문입니다.

다만, Multisize 기법이나 Horizontal flip을 사용하게 되면 성능이 굉장히 많이 올라 갑니다.

> Object detection에서 TTA

![](https://user-images.githubusercontent.com/31475037/123568573-93b9ed00-d7ff-11eb-8317-2eb793154155.png)

<br>

## Post processing in Faster R-CNN

보통 많이 사용되는 Faster R-CNN 계열 모델의 경우 TTA 사용시 다음의 과정을 거쳐 bbox를 검출해 냅니다.

### 1. Augmentation

원본 이미지에 대해 augmentation을 합니다.

### 2. Inference

원본 이미지, augmentation 이미지에 대해 각각 inference 해줍니다. 

### 3. Bbox aggregation

inference 결과로 나온 모든 bbox를 모아줍니다.

### 4. Confidence score threshold

Faster R-CNN 모델이 bbox에 대해 예측을하고, 각 bbox에 대한 confidence score를 예측합니다. 예측한 값 중에서 confidence score threshold 보다 낮은 값은 bbox로 사용하지 않습니다.

### 5. Non-Maximum Suppression(NMS)

남은 bbox 중 겹치는 bbox를 제거하는 과정이 NMS(Non Maximum Supression) 과정입니다. Bbox 끼리 IOU가 NMS threshold보다 높으면, confidence score가 가장 높은 bbox만 남깁니다.

> NMS threshold

![](https://user-images.githubusercontent.com/31475037/123568571-93215680-d7ff-11eb-8375-39a01e6f2cfc.jpg)

