---
layout: post
title: Normalization
tags: [ML]
use_math: true
---

이 포스트에서는 Normalization 기법에 대해 간단히 리뷰해 보겠습니다.

> 그림으로 표현된 Norm 기법

![](https://bloglunit.files.wordpress.com/2018/04/ec8aa4ed81aceba6b0ec83b7-2018-04-11-ec98a4ed9b84-3-07-41.png?w=1024)

<br>

## Batch Normalization

이 정규화 방법은 Gradient Vanishing / Gradient Exploding이 일어나지 않도록 하는 아이디어 중 하나입니다. 

- Gradient Vanishing : 신경망에서 손실 함수의 gradient 값이 0에 근접하여, backpropagation이 잘 되지 않는 문제
- Gradient Exploding : error gradient가 축적되어 가중치 업데이트 시 overflow 되는 현상

<center><img src="https://user-images.githubusercontent.com/31475037/59975969-7b53de00-95f9-11e9-9877-677945acba24.png" width="50%"></center>
### Internal Covariance Shift

Batch Norm 논문에서는 위에서 언급된 문제의 근본적인 원인이 Internal Covariance Shift라고 주장했습니다.

이는 네트워크의 각 층이나 활성 함수마다 입력의 distribution이 계속 바뀌는 현상을 의미합니다.

![](https://qph.fs.quoracdn.net/main-qimg-962c827a0730a481d35bca4d41fe0251)

> Batch Normalization

<center><img src="https://cdn-images-1.medium.com/max/1600/1*Hiq-rLFGDpESpr8QNsJ1jg.png"></center>
- mini-batch의 평균 계산
- mini-batch의 분산을 1로, 평균을 0으로 만들어줌(normalize)
- 단순히 분산=1, 평균=0으로 고정하면 activation fucntion의 nonlinearity를 없앨 수 있기에 trainable parameter인 ᵞ(감마)과 β(베타)를 추가해줌 

### batch normalization이 잘되는 이유

- 신경망에서 가중치의 변경은 그와 연결된 layer에 영향을 미칩니다.
- 이는 가중치의 변화는 매우 복잡하게 신경망에 영향을 미칩니다.
- 그렇기 때문에 Vanishing 또는 Exploding을 막기 위해 small learning rate를 사용하거나, 활성화 함수 사용을 선택합니다.
- Batch Norm은 최적화를 하기 쉽게 만들기 위해 네트워크를 reparameterization합니다.
- 이 방법은 모든 레이어의 평균, 크기, 활성화 함수를 독립적으로 조정할 수 있게 만듭니다.

Batch Normalization이 어떻게 Optimization에 도움을 주는지는 [PR-134](https://www.youtube.com/watch?v=hiN0IMM50FM&t=808s)강의에서 잘 설명하고 있습니다.

<br>

## Layer Normalization

BN과 LN은 거의 유사한 형태를 지닙니다. 위의 큐브 사진 또는 아래이 사진이 이를 시각화한 모양입니다

<center><img src="https://user-images.githubusercontent.com/31475037/59975970-7b53de00-95f9-11e9-90be-30b16e722568.png" width="50%"></center>
BN의 경우에는 mini-batch 단위로 계산이 이뤄지며, 각 mini-batch에서 동일하게 계산합니다. 하지만 LN의 경우에는 각 feature 별 독립적으로 계산됩니다.

LN은 batch 사이즈와 상관이 없습니다. 이런 특성으로 RNN에서 좋은 성능을 보입니다.

<br>

## Instance Normalization

Instance Norm은 LN과 비슷하지만 한 단계 더 나아가 평균과 표준 편차를 구하여 channel 단위의 normalization을 진행합니다.

Style Transfer에서 좋은 성능을 냈습니다.

작은 batch에 효과적이어서 CycleGAN같은 모델에서 좋은 효과를 냈습니다.

<center><img src="https://user-images.githubusercontent.com/31475037/59975971-7b53de00-95f9-11e9-9d28-46b698042ee9.png" width="50%"></center>
<br>

## Group Normalization

IN과 LN의 중간입니다. 여기서는 채널을 그룹으로 묶어 평균과 표준편차를 구한다는 점입니다. 모든 채널이 단일 그룹에 있다면 LN, 각 채널을 다른 그룹에 배치하면 IN 입니다.

LN과 IN은 모두 공통적으로 RNN과 Style Transfer에 효과적이지만, BN에 비해 이미지 인식면에서는 성능이 좋지 못합니다.

Group Norm의 경우 ImageNet Classification task에서 batch size가 32일 때 BN과 거의 근접한 성능을 냈고, 그 보다 작은 batch size에 대해서는 더 좋은 성능을 냈습니다. (BN의 경우 batch size가 작아지면 성능이 안좋아짐)

왜 GN이 LN 혹은 IN보다 효과적일까요?

그것은 GN이 여러 채널에서 서로 다르게 계산하기에 모델에 따른 유연성을 제공하기 때문입니다. 

<center><img src="https://user-images.githubusercontent.com/31475037/59975972-7bec7480-95f9-11e9-95a8-f8cec354e3a9.png" width="50%"></center>
<br>

## Image의 Style과 Content

Image는 크게 Style정보와 Content정보로 나뉘고 이를 기반으로 어떻게 학습을 시킬까에 대한 연구가 이루어지고 있습니다.

[Batch-Instance Normalization](https://blog.lunit.io/2018/05/25/batch-instance-normalization/) 글과 [딥러닝의 비밀](https://www.youtube.com/watch?v=KUs3xng6ags) 유튜브 강의에서 이에 대해 잘 설명해주고 있습니다.

<br>

**읽어볼만한 글**

[Group Norm](https://blog.lunit.io/2018/04/12/group-normalization/)

