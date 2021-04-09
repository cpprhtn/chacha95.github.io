---
layout: post
title: CNN 모델
tags: [Efficeint DL]
use_math: true
---

## CNN 모델 리뷰

2012년도 이후 나온 중요한 CNN 모델에 대해 간략히 리뷰합니다.

이 포스트에서 모델의 depth(깊이)는 convolution layer의 개수로 셉니다.

### AlexNet

AlexNet은 2012 ILSVRC(ImageNet challenge)에서 우승한 모델입니다.

해당 모델의 우승으로 소수의 그룹만 연구하던 딥러닝이라는 분야가 컴퓨터 비전을 연구하는 그룹들에 널리 알려졌습니다.

> 2012 ILSVRC 우승

<center><img src="https://user-images.githubusercontent.com/31475037/63636148-9351e780-c6a6-11e9-8f70-cd9aca70032f.PNG"></center>
AlexNet의 구조는 얀 르쿤 교수가 1998년에 제안한 LeNet5 구조에서 큰 변화는 없었고, Convolution filter size의 크기 변화 정도만 있었습니다.

기본 구조는 2개의 GPU를 기반으로 한 병렬 구조입니다.  학습을 위해 GTX580 두개를 사용했으며, 구조적으로 3GB의 메모리에 맞게 설계되어 있습니다. (GTX 580 하나당 1.5G)

> AlexNet 구조

<center><img src="https://miro.medium.com/max/1398/1*wzflNwJw9QkjWWvTosXhNw.png"></center>
**group convolution**

AlexNet에서는 채널을 2개로 나누어 convolution을 진행합니다.

특이한 점으로는 AlexNet의 첫번째 layer를 분석했을 때, 위 그룹은 외곽선 위주, 아래 그룹은 채널위주의 학습을 합니다.

연구팀은 group을 2개로 나누었을 때 가장 좋은 성능을 낸다는 것을 알아냈습니다.

이후 이러한 방법론이 발전해 ResNext라는 모델이 나오게 됩니다.

> 외곽선 위주의 채널 학습한 그룹(위), 색상, 패턴 위주의 채널 학습한 그룹(아래)

<center><img src="https://user-images.githubusercontent.com/31475037/63742179-f0ca7c00-c8d2-11e9-971d-12352d7dd3e3.PNG"></center>

**주요 Contribution**

- 의미있는 성능을 낸 첫 번째 CNN 모델
- 요새는 흔히 쓰이는 drop out 기법이나, 여러 전처리 기법을 사용
- pooling layer의 적극적인 사용
- group convolution의 사용 
- non-linearity function인 ReLU의 사용

<br>

### VGGNet

2014년 ILSVRC에서 준우승한 모델입니다.

챌린지에서 준우승을 하긴 했지만, 간단한 구조와 이해와 변형이 쉬워 오히려 1등을 한 GoogleNet보다 더 많이 사용되었습니다.

> 2014 ILSVRC 준우승

<center><img src="https://user-images.githubusercontent.com/31475037/63636149-9351e780-c6a6-11e9-8451-7b9f0d9ae52f.PNG"></center>
VGG Net 모델의 가장 큰 특징은 바로 convolution filter 크기를 3x3으로 고정하고, 망의 깊이가 깊어졌다는 것입니다.

VGGNet 연구팀은 논문에서 모델의 depth가 어떤 영향을 주는지 연구하기 위해 개발을 했다고 언급했습니다.

오직 깊이가 어떤 영향을 주는지 아기 위해 convolution filter 크기는 가장 간단한 3x3으로 정하고, 깊이의 영향에 대해 실험을 했습니다.

VGGNet은 크게 2가지로 VGG16, VGG19두가지이며, 굉장히 심플함에도 불구하고 엄청난 성능을 냈습니다.

> VGGNet 구조

<center><img src="https://user-images.githubusercontent.com/31475037/63636150-9351e780-c6a6-11e9-9f93-65ca4c1809c5.PNG"></center>
**주요 Contribution**

- 모델이 깊을 수록 성능이 좋다는 것을 보여줌
- 간단한 구조로 많은 연구자들이 사용함

<br>

### GoogLeNet(Inception)

2014 ILSVRC에서 우승한 모델입니다.

CNN의 성능향상을 위한 가장 직접적인 방법은 모델의 크기를 늘리는 방법입니다.

모델의 크기를 늘리는 방식은 크게 두가지로 모델의 depth를 늘리거나, 모델의 width를 늘리는 방식입니다.

이러한 모델의 크기가 늘어남에 따라 학습해야할 parameter가 증가하게 되며,  이에따라 overfitting이 일어날 가능성도 높아지게 됩니다. 또한 train 및 모델 사용시 계산량이 늘어나게되는 문제 또한 생기게됩니다.

이러한 계산량 및 학습시킬 parameter를 줄이기 위한 여러 기법들이 제안된 논문입니다. 실제로 AlexNet에 비해 1/12개의 parameter만을 가지고 있습니다.

> 2014 ILSVRC 우승

<center><img src="https://user-images.githubusercontent.com/31475037/63636149-9351e780-c6a6-11e9-8451-7b9f0d9ae52f.PNG"></center>
GoogleNet에서 같은 layer에 서로 다른 크기를 갖는 convolution filter를 적용해 다양한 feature를 얻을 수 있도록 했습니다. 이러한 방식을 모델의 width를 넓힌다고 말합니다.

본 연구팀이 제안한 모듈을 Inception module이라 불렀으며, Inception v2~v4까지 다양한 버전으로 추후 만들어졌습니다.

> GoogleNet 구조


<center><img src="https://user-images.githubusercontent.com/31475037/63636151-93ea7e00-c6a6-11e9-8be1-7ab68413f35a.PNG"></center>
GoogLeNet 팀에서 제안한 핵심적인 모듈은 바로 Inception module 입니다.

일반적인 딥러닝 모델에서는 한 레이어에서 하나의 conv filter만 쓰입니다.

하지만 본 연구팀에서는 한 layer에서 다양한 종류의 filter나 pooling을 이용하는 방식을 제안합니다.

> (naive) Inception module

<center><img src="https://i.imgur.com/VY3BkBR.png"></center>
**1x1 conv**

GoogleNet의 Inception Module에서 가장 핵심적인 부분은 바로 1x1 Conv를 이용해 Dimensionality Reduction(차원감소)를 한게 아닐까 싶습니다.

1x1 Conv 사용시 출력 채널의 수가 입력채널의 수보다 작아지게 되면, DR 효과가 있기 때문입니다.

본 연구팀은 NIN(Network-In-Network)논문에서 큰 영향을 받았고, 그에 따라 1x1 Conv를 사용했다고 말합니다.

<center><img src="https://user-images.githubusercontent.com/31475037/59593122-527ca600-912c-11e9-9668-d8ca2e3e586f.png"></center>
이러한 철학이 적용된 후 Inception module은 다음과 같은 구조로 바뀝니다.

> 1x1 적용 이후

<center><img src="https://user-images.githubusercontent.com/31475037/63636153-93ea7e00-c6a6-11e9-887f-b813cd926bc4.PNG"></center>
1x1 Conv를 이용한 Dimensionality Reduction이 적용된 후 총 파라미터 수는 다음과 같이 변합니다.

Naive Inception module에서는 총 파라미터 수가 865M개이지만, 1x1 Conv가 적용된 module에서는 358M개의 파라미터만 존재합니다.

> parameter 수

![](https://user-images.githubusercontent.com/31475037/63636154-93ea7e00-c6a6-11e9-94a0-2ec285cdba44.PNG)





**주요 Contribution**

- 모델 경량화에 대한 중요한 실험과 구조를 제안(1x1 Conv)
- depth 뿐만 아니라 width가 성능에 미치는 부분을 보여줌

<br>

### ResNet

2015 ILSVRC 우승한 모델입니다.

ResNet은 기존 모델들이 가지고 있었던 depth에 대한 한계를 깨부순 모델이며, 현재까지도 대다수의 모델들이 ResNet을 기반으로 파생된 모델입니다.

> 2015 ILSVRC

<center><img src="https://user-images.githubusercontent.com/31475037/63636156-94831480-c6a6-11e9-8821-ad0e132ea12e.PNG"></center>
기존 모델들이 depth를 늘리면서 마주치는 문제는 크게 두가지 였습니다.

- Vanishing/Exploding Gradient 문제: 모델이 깊어지게 되면서 Gradient의 전달자체가 굉장히 어렵게 되는 문제가 발생했습니다.
- 모델이 깊어지게 되면서, 파라미터가 굉장히 많이 늘어나 overfitting 문제가 대두 되었습니다.

모델의 depth를 늘리면 늘릴 수록 성능이 오르길 기대했으나, 정 반대의 현상을 나타냈습니다.

아래 그림은 Cifar-10 데이터셋에 대해 학습을 한 경우인데, layer가 늘어났음에도 불구하고 56-layer의 loss가 20-layer의 loss에 비해 낮은걸 볼 수 있습니다.

> Cifar-10 데이터셋에 따른 성능 차이

<center><img src="https://user-images.githubusercontent.com/31475037/63676536-1cdbf380-c826-11e9-8da5-45163d51b2fa.PNG"></center>
이러한 depth를 늘렸음에도 backpropagation이 잘 안되는 문제를 타계하기 위해, 연구팀은 Residual Learning을 제안합니다.

아래 그림과 같이 Residual Block에서 입력과 출력에 있어 입력을 그대로 출력에 더해주어 backpropagation이 잘 되도록 만들었습니다. 이러한 연결을 identity mapping이라 부릅니다.

> Residual Learning

<center><img src="https://user-images.githubusercontent.com/31475037/63676533-1cdbf380-c826-11e9-8ba6-40c494e45819.PNG"></center>
**주요 Contribution**

- 딥러닝 모델링에 지대한 영향을 미침
- 모델의 depth를 극한으로 깊게 만들 수 있는 방법론 제안
- ResNet BottleNeck 구조가 연산량 감소에 굉장히 효과적



<br>

### ResNext

ResNext는 FAIR 그룹에서 제안된 모델로, AlexNet에서 제안한 group convolution 개념을 확장했습니다.

기존의 CNN 모델링에서 width, depth 두가지만을 많이 고려했다면, ResNext부터는 cardinality라는 개념을 제안했습니다.

예를들어 입력 채널이 256채널이고, Cardinality가 32이면, 8 채널씩 나눠서 group convolution을 진행하는 구조입니다.

> ResNext 구조

<center><img src="https://user-images.githubusercontent.com/31475037/63677283-9aecca00-c827-11e9-848f-ea9f5b1ef7d7.PNG"></center>
**주요 Contribution**

- cardinality 개념을 제안함



<br>

**참고강의**

CS231N