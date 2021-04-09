---
layout: post
title: Detectron2 사용법
tags: [Object Detection]
use_math: true
---

[이전 포스트](https://chacha95.github.io/2020-04-28-Object-Detection5/)를 읽고 오시면 이해에 도움이 됩니다.

이 포스트는 detectron2이 어떤식으로 구동이 되는가에 대한 설정들에 대해 다룹니다.

# Data

- built in dataset으론 VOC와 COCO 등등의 대표적인 dataset들이 존재함

- custom dataset을 detectron2에 등록해서 사용 가능 → 학습 때마다 따로 등록하도록 구현하는걸 권장 하지만 build해서 built in 가능

- 이미지를 읽을 때 opencv를 이용해 읽어옴 → PIL이 속도가 느리기 때문에 -> [관련 포스트](https://chacha95.github.io/2019-08-01-PIL-vs-OpenCV/)

- dataloader에서는 PIL을 이용해 이미지 전처리(그 과정에서 BGR -> RGB로 바꿔줌)

- 이미지 전처리시 crop을 할 수 있으나 기본 설정은 crop 기능을 사용하지 않음

<br>

# Train/Test

- model zoo에 pretrained 모델 및 config 정보가 yaml 파일에 담겨 있음

- training loop가 FAIR 에서 만든 엔진을 통해 이루어지며 추상화가 엄청나게 되있고 내부적으론 C로 구현, python wrapper를 통해 불러오는 형식을 취해서 이해하기 어려움. 하지만 plain_train.net은 training loop에 변경이 가능케 쉽게 써져 있음. 그렇지만 plain_train.net은 기본 SGD를 이용한 정말 기본 학습 방법론이여서 성능 측면에서 썩 좋지 않은 결과를 보임

- 학습시 입력 이미지 사이즈가 동일하다면 cudnn benchmark를 true로 두는 것이 좋다고 함 -> [답변글](https://github.com/facebookresearch/detectron2/issues/359) -> FAIR 측에선 COCO dataset으로 학습했기에 입력 이미지 크기가 다 달라서 cudnn benchmark의 default값이 false임

- 학습 진행 사항은 tensorboard를 통해 확인 가능

- 내부 training policy는 FAIR에서 제안하는 [논문](https://research.fb.com/wp-content/uploads/2017/06/imagenet1kin1h5.pdf)에 기반하여 진행되었음. 기본적으로 detectron2에선 batch size 2당 gpu 1개 할당을 추천 → FAIR에선 V100 gpu 8개 nvlink 달린 workstation에서 gpu 8개를 사용하면서 batch size 16으로 학습 

- detectron2의 configuration은 [fvcore](https://github.com/facebookresearch/fvcore)라는 자체 오픈소스를 통해 관리됨, 모델에 대한 정보는 model zoo 폴더에 yaml로 관리되고 있고, yaml 파일을 읽어 fvcore에 로딩하는 방식을 채택함

- Evaluation의 경우 COCO API를 가져다가 약간의 custom만 한 형태를 가짐 -> COCO 평가 방식을 따름

- detectron2의 오버헤드가 큰 부분들을 CUDA로 구현(deformable conv나 NMS threshold 등등)해 성능 향상을 이룸 -> 이 때문에 detectron2를 git을 통해 다운 받은다음에 ninja를 통해 빌드해야 사용가능 -> 개인적인 추천으론 docker-compose로 구현된 파일 다운받아 사용하는게 가장 편리 했음

- 전체적으로 오버헤드가 걸리는 부분을 전부 CUDA로 구현했음. 이를 통해 기존에 있던 다른 오픈소스 대비 [월등한 성능](https://detectron2.readthedocs.io/notes/benchmarks.html)을 보였음

<br>

# bbox mode 관련

- detectron2에는 bbox 관련 다양한 모드 설정이 존재 

- custom dataset 등록시, metadata를 입력하면서 변경 가능

- [detectron2 doc](https://detectron2.readthedocs.io/modules/structures.html#detectron2.structures.BoxMode)에 모델 예측 결과 값들을 class에 담아 반환 함

> detectron2 BoxMode에 관한 정보

<center><img src="https://user-images.githubusercontent.com/31475037/89261584-483ba500-d669-11ea-99e0-e124f74e4869.png" width="90%"></center>

<br>