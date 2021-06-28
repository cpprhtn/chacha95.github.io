---
layout: post
title: data augmentation 소개
tags: [Data Augmentation]
use_math: true
---

# What is Data augmentation?

보통 머신 러닝 학습시 데이터 추가에 많은 비용과 시간이 들기에 데이터 개수를 증강시키는 **data augmentation** 기법을 씁니다. Data Augmentation은 기존 데이터에서 augmentation된s 데이터를 생성해 데이터셋 크기를 인위적으로 확장합니다.

<br>

# Methods

## Geometric

Geometric augmentation의 방법으론 flip, crop, rotate, multisize로 이미지를 만드는 방법이 있습니다.

> 이미지 자체를 자르거나 크기를 수정함

![](https://user-images.githubusercontent.com/31475037/123568246-e34be900-d7fe-11eb-97b2-569aed7227d9.png)

<br>

## Color

Color augmentation의 방법으론 color(RGB or HSV) 값을 증가시키거나 감소 시키는 방법이 있습니다.

> HSV space에서 augmentation

![](https://user-images.githubusercontent.com/31475037/123568243-e2b35280-d7fe-11eb-9147-a1f68810262a.png)

<br>

## Kernel filters

Kernel filter augmentation의 방법으론 sharpen, blur 와같은 필터를 씌우는 방식이 있습니다.

> filter를 통한 방식

![](https://user-images.githubusercontent.com/31475037/123568242-e21abc00-d7fe-11eb-964d-e18aed82b9a5.png)

<br>

## Erasing

image에 random 크기의 bounding box를 만든 뒤 그 안을 random noise, ImageNet mean value, [0, 0, 0], [255, 255, 255] 값 등으로 채워서 학습하는 방식입니다.

> [255, 255, 255] bounding box 생성

![](https://user-images.githubusercontent.com/31475037/123568239-e1822580-d7fe-11eb-8350-af7b384cfade.png)

<br>

**참고 자료**

[Data Augmentation in Python: Everything You Need to Know - neptune.ai](https://neptune.ai/blog/data-augmentation-in-python)

[Papers with Code - Image Augmentation](https://paperswithcode.com/task/image-augmentation)

[Image Data Augmentation Overview](https://hoya012.github.io/blog/Image-Data-Augmentation-Overview/)
