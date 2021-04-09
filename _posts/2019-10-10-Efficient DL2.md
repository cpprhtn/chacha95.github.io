---
layout: post
title: Efficeint DL
tags: [Efficeint DL]
use_math: true
---

# 모델 경량화 방법

이번 포스트에서는 CNN의 문제점과 이를 해결하기 위한 방안, 또 CNN을 Light(가볍게)하게 만드는 방법에 대해 알아봅시다. Quantization, Prunning, Distilltion 측면에서의 Efficeint DL이 아닌, Architecture 측면에서의 Efficeint DL을 리뷰합니다.

## Problem of CNN

일반적인 2D Convolution을 기반으로한 CNN 모델에서는 다음과 같은 문제점이 존재합니다.

- **Expensive Cost**

  네트워크는 점점 깊어지고, 채널의 수가 증가하고 있습니다. 그에따라 연산량과 학습해야할 parameter가 비례해서 증가하고 있습니다.

  다만 네트워크가 깊어짐에 따라 receptive field도 증가함으로서 네트워크가 더 넓은 영역을 볼 수 있게 됬습니다.

  대다수의 모델에서 이러한 연산량과 학습해야할 parameter의 수를 줄이기 위해 pooling을 사용합니다.

  채널의 수를 늘릴수록 더 많은 filter를 학습 할수 있지만, 그에따라 학습해야할 parameter 수 증가, 학습속도의 하락, overfitting 문제가 산재해 있습니다.

- **Dead Channels**

  학습 과정에서, 출력 결과에 영향을 거의 주지 않는 정점이 나타납니다.

  이러한 문제로인해 모델의 연산량이 계속해서 증가하게 됩니다.

  > Dead Channels의 예시, 초록색은 영향을 미치는 채널, 회색은 영향이 거의 없는 채널

  <center><img src="https://user-images.githubusercontent.com/31475037/63741860-a85e8e80-c8d1-11e9-8ee1-39abf98e92c5.PNG"></center>

- **Low Correlation between channels(sparse correlation matrix)**

  모든 채널-필터 쌍이 높은 correlation을 가질 수 만은 없습니다.

  sparse한 correlation matrix 생성이 되면 연산량이 늘어나지만, 효율성은 떨어집니다.
  
  

이러한 문제점을 해결하기 위해 제안된 방법에 대해 알아봅시다.

<br>

## Dilated Convolution

영상 내의 Objects에 대한 정확한 판단을 위해서는 Contextual Information이 중요합니다.

- 객체 주변의 배경은 어떤가?
- 객체 주변의 다른 객체들은 어떤 종류인가?

만일 receptive field가 충분히 확보되지 않았다면 영상의 일부분만 보고 판단을 하게되고, 이로인해 머핀과 치와와를 구분하지 못하는 일이 생기게 됩니다.

> Receptive Field의 중요성ㅛ

<center><img src="https://user-images.githubusercontent.com/31475037/63749576-b2d75300-c8e6-11e9-83ff-d42f545ed8f1.PNG"></center>
충분한 Contextual Information 확보를 위해 넓은 Receptive field가 중요하다가 바로 요점입니다.

일반적으로 CNN에서 Receptive Field 확장을 위해선 두가지 방법이 쓰입니다.

- kernel size의 확장
- 더 많은 Conv layer 사용

하지만 두 방법 모두 연산량을 크게 증가시키는 단점이 있습니다.

그래서 나온 방식이 바로 Dilated Convolution입니다. Dilated Conv는 Conv 필터에 간격을 둔 filter입니다.

입력 픽셀 수는 동일 하지만, 더 넓은 범위에 대해 입력 수용 가능합니다.

> Dilated Convolutions

<center><img src="https://cdn-images-1.medium.com/max/1200/1*SVkgHoFoiMZkjy54zM_SUw.gif"></center>
Dilated Convolution은 필터 내부에 padding을 추가해 강제로 recpetive field를 늘리는 방식입니다.

위 그림에서 진한 파란 부분만 weight가 있고 나머지 부분은 0으로 채워집니다.

pooling을 수행하지 않고도 receptive field를 크게 가져갈 수 있습니다.

<br>

## Convolution Factorization

Convolution Factorization을 통해 receptive field의 크기는 유지한채 계산량을 줄일 수 있습니다.

> Factorization

![](https://user-images.githubusercontent.com/31475037/63760100-ae686580-c8f9-11e9-9501-0c739d8bde25.PNG)



이러한 Factorization이 적용된 것이 Inception v2입니다. 

> Inception module의 Fatorization

![](https://user-images.githubusercontent.com/31475037/63759870-369a3b00-c8f9-11e9-869d-0ae2902f7ed0.PNG)

이러한 아이디어는 더 발전해, Inception-v3에서는 asymmetric convolution을 이용한 factorization 기법을 이용해 망의 깊이와 연산량을 줄였습니다.

> Inception-v3의 asymmetric convolution factorization

![](https://user-images.githubusercontent.com/31475037/66735133-795fa480-eea0-11e9-9c79-c73b9c8b867e.PNG)

<br>

## Point-wise Convolution

Point-wise Convolution은 커널의 크기가 1x1로 고정된 Convolution layer를 말합니다.

Point-wise Convolution은 하나의 필터는 채널 별로 Coefficient를 가지는 Linear Combination으로 볼 수 있습니다.

> 1x1 Conv

<center><img src="https://user-images.githubusercontent.com/31475037/59593122-527ca600-912c-11e9-9668-d8ca2e3e586f.png"></center>
일반적으로는 Dimensionality Reduction(DR) 기법으로 많이 사용합니다. 왜냐하면 출력 채널의 수가 입력 채널의 수보다 적게 되면, DR의 효과가 있기 때문입니다. 이러한 특성을 이용해, 전체 채널수를 줄여 Dead Channel 문제를 해소합니다.

> 채널수를 줄여 Dead Channel 문제 해소

![](https://user-images.githubusercontent.com/31475037/63741937-00959080-c8d2-11e9-8fa2-e40d468421f7.PNG)



또한 point-wise Conv는 여러 구조에서 실전적으로 적용되어, 경량화와 성능 개선이 가능함을 실험적으로 증명하였습니다.

<br>

## Group Convolution

Group Convolution은 입력 영상의 채널들을 여러개의 그룹으로 나누어 독립적인 Convolution을 수행하는 방식입니다.

이 아이디어는 AlexNet에서 처음 제안되었으며, 이후 ResNext 구조에서 Cardinality의 개념으로 확장됩니다.

> Group Conv

![](https://user-images.githubusercontent.com/31475037/63742178-f0ca7c00-c8d2-11e9-84b3-654c675f75d4.PNG)

AlexNet에서 첫번째 convolution layer를 분석해 보았을 때, 각각의 그룹이 서로 다른 특성을 학습을 하는것을 확인할 수 있습니다.n

즉, 각 그룹마다 독립적인 feature의 학습을 기대할 수 있습니다.

> 외곽선, 음영 위주 학습(위), 색상, 패턴 위주 학습(아래)

<center><img src="https://user-images.githubusercontent.com/31475037/63742179-f0ca7c00-c8d2-11e9-971d-12352d7dd3e3.PNG"></center>
또한 그룹 수를 조정하면 성능과 parameter의 수가 변경되는데, 이는 사용자가 HyperParameter로서 조정할 수 있습니다.

group convolution을 이용해 파라미터 수를 줄이면서, 모델 성능을 높일 수 있습니다.

> AlexNet 논문에서 나온 group 수에 따른 결과 차이

<center><img src="https://user-images.githubusercontent.com/31475037/63742181-f0ca7c00-c8d2-11e9-8fb3-65d078af3a86.PNG"></center>
<br>

## Depth-wise Convolution

일반적인 Convolution Filter는 입력 영상의 모든 채널의 영향을 받게 됩니다. 즉, 완벽히 특정 채널만의 Spatial Feature를 추출하는 것은 불가능 합니다. Depth-wise Convolution은 각 단일 채널에 대해서만 수행되며, 각 채널의 Spatial Feature에만 집중합니다.

<center><img src="https://user-images.githubusercontent.com/31475037/63750785-1793ad00-c8e9-11e9-9157-4f5489a9c708.PNG"></center>
<br>

## Depth-wise Separable Convolution

기존의 Convolution에서 Cross-channel Correlation을 완벽히 분리하기 위해 제안된 구조입니다. 각 채널 별 Spatial Convolution 이후 Channel별 Linear Combination을 진행합니다. 기존의 convolution과 비슷한 작용을 하지만 계산량에 있어 현저히 적은 계산 비용을 보입니다.

<center><img src="https://user-images.githubusercontent.com/31475037/63750783-1793ad00-c8e9-11e9-9202-c6af86ccb1a6.PNG"></center>
<br>

## Xception

Xception 구조는 Inception 모듈과 Depth-wise separable convolution의 중간에 있는 모델로 계산 비용에 있어 이점과 함께, 기존 모델들에 비해 성능 측면에서도 좋은 성능을 냈습니다. 

특히나 Xception은 cross-channel correlation과 spatial correlation의 완전히 분리가 가능하다는 가설에 의해 설계되었습니다. 이는 depth-wise separable convolution을 이용해 구현 하였습니다.

[PR-34 ](https://www.youtube.com/watch?v=V0dLhyg5_Dw)강의에서 Inception 모듈에 대한 기본적인 철학부터 xception까지 잘 설명해 주고 있습니다.

> Inception 구조의 Width를 극한으로 늘린 케이스

<center><img src="https://user-images.githubusercontent.com/31475037/66801254-6c4fbd80-ef53-11e9-8deb-0b7d1159408e.PNG"></center>
<br>

## SqueezeNet

SqueezeNet은 MobileNet이 나오기 이전까지는 EfficientNet의 대명사로 불렸던 모델입니다. SqueezeNet에서 제안하는 모델 설계 전략은 다음과 같습니다.

1. **3x3 conv를 1x1 conv로 바꿈**

   이를 통해 모델의 연산량과 파라미터를 줄입니다. 모델의 연산량과 파라미터를 줄인다는 것을 모델의 사이즈를 줄인다고도 부릅니다.

2. **입력 채널의 수를 줄여줍니다**

   Bottleneck 구조와 비슷합니다. 이를 통해 입력 채널의 수를 줄여 연산량과 파라미터 수를 줄입니다.

3. **Downsampling을 하는 부분을 모델의 뒤에서 해줍니다**

   Downsampling을 뒤에 하는 부분은 모델 사이즈를 줄이는 방법은 아니고, 정확도(accuracy)를 유지하기 위한 방법입니다.

위의 1,2번 아이디어를 차용해 만들어진 것이 Fire Module 입니다. 1x1 conv으로 입력 채널의 수를 줄인 뒤, 3x3 conv와 1x1 conv로 이루어진 레이어에서 채널 수를 늘려주는 방식입니다.

> The Fire Module

<center><img src="https://user-images.githubusercontent.com/31475037/63752110-a0abe380-c8eb-11e9-8a02-61d41979c96a.PNG"></center>
저자는 이런 Fire Module을 적용해 3가지의 SqueezeNet 구조를 제안합니다.

> The Fire Module을 적용한 SqueezeNet 구조

![](https://user-images.githubusercontent.com/31475037/66811154-90b79400-ef6b-11e9-83fa-2c586d06f255.PNG)

설계에 따른 결과는 다음과 같습니다.

> 모델에 따른 성능 차이, SqueezeNet + Simple Bypass가 성능이 가장 좋음

![](https://user-images.githubusercontent.com/31475037/66811153-90b79400-ef6b-11e9-9009-a8b5f4028c90.png)



<br>

## MobileNet

다음의 세가지 아이디어를 사용한 네트워크 설계입니다.

1. **point-wise convolution의 적극 활용**

   point-wise convolution(Conv 1x1)을 적극 활용해서 전체적인 연산량과 파라미터 수는 줄이면서, 망을 깊게 유지 할 수 있었습니다. MobileNet에서는 Conv1x1이 모델에서의 연산과 파라미터의 대다수를 차지 합니다.

   > parameter 수와, 곱/합 연산의 비중

   <center><img src="https://user-images.githubusercontent.com/31475037/66800847-d2d3dc00-ef51-11e9-8b7d-73a8f1d1ac91.PNG"></center>

2. **depth-wise separable convolution의 활용**

   Depth-wise separable convolution을 이용하면 일반 convolution보다 연산량이 8~9배 적게 듭니다.

   > depth-wise separable convolution

   ![](https://user-images.githubusercontent.com/31475037/66801255-6ce85400-ef53-11e9-88c2-465921624df2.PNG)

   이 depth-wise separable convolution을 이용하여 MobileNet에선 다음과 같은 레이어 구조를 사용하였습니다.

   > 왼쪽은 기존 레이어, 오른쪽은 MobileNet이 제안하는 레이어

   <center><img src="https://user-images.githubusercontent.com/31475037/66800848-d2d3dc00-ef51-11e9-83d2-2f759ae3d356.PNG"></center>

3. **resolution과 channel size 보정을 통한 모델 경량화**

   - width multiplier 𝛼 : 각 레이어의 입출력 채널의 수를 감소시키는 파라미터

   - resolution multiplier 𝜌 : 각 레이어의 입력 해상도를 감소시키는 파라미터

   두 하이퍼 파라미터를 도입하여 모델을 경량화하는 시도를 하였습니다.



[PR-44](https://www.youtube.com/watch?v=7UoOFKcyIvM) 강의에서 잘 설명해주고 있습니다.

<br>

## ShuffleNet

ShuffleNet은 group convolution의 아이디어를 이용해 간단하면서도 좋은 성능을 가진 모델 설계를 제안합니다.

Groupe conv를 적층하게되면, 독립적인 네트워크처럼 grouped된 채널만 보는 문제가 생기게 됩니다. 이런 문제를 해결하기 위해, 모델이 여러 grouped된 채널을 보기 위해 channel shuffle을 해줍니다. Channel shuffle은 무작위로 하는것이 아닌, 규칙을 가지고 섞어줍니다. 이를 통해, 간단한 구현으로 그룹간 independency 해결합니다. 또한 Grouped Conv로 Sparse Correlation Matrix 문제를 해결함으로써 성능을 높였습니다.

> channel shuffle

![](https://user-images.githubusercontent.com/31475037/66800845-d23b4580-ef51-11e9-9b35-94e51fe41af9.PNG)



저자는 ShuffleNet Unit이라는 구조를 제안하고 이를 기반으로 모델을 여러조건에서 실험을 하였습니다.

> ShuffleNet Units

![](https://user-images.githubusercontent.com/31475037/66800846-d23b4580-ef51-11e9-8f1f-473ea9d7c56e.PNG)

저자 팀은 scale factor를 추가해, 모델의 전체적인 채널수를 조절 했을 때를 실험했습니다. 실험을 통해  scale factor로 인해 바뀌는 전체 채널 수에 따라, 적절한 group의 수를 결정해야 한다는 사실을 알게 됩니다.

> scailing factor와 group 수에 따른 성능 차이

<center><img src="https://user-images.githubusercontent.com/31475037/66823013-4b519180-ef80-11e9-8b98-025e87baa9cc.PNG"></center>
또 언제나 group을 shuffle 했을때가 성능이 좋았습니다.

> Shuffle을 했을 때가 성능상으로 항상 좋음

<center><img src="https://user-images.githubusercontent.com/31475037/66823011-4b519180-ef80-11e9-80dc-48b190b8421e.PNG"></center>
> 다른 네트워크와의 연산량 비교

<center><img src="https://user-images.githubusercontent.com/31475037/66823012-4b519180-ef80-11e9-9378-f8d283d9c507.PNG"></center>
<br>

**참고 강의**

[blog.yani.io](https://blog.yani.io/filter-group-tutorial/)

[slideshare](https://www.slideshare.net/ssuser6135a1/ss-106656779)

<br>

