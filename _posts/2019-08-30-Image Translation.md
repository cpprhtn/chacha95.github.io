---
layout: post
title: CycleGAN 리뷰
tags: [Image Translation]
use_math: true
---

이번 포스트에서는 unsupervised learning의 대표 모델인 CycleGAN모델에 대해 적겠습니다.

CycleGAN은 Image Translation의 대표 기술이며, CVPR 2017에 발표된 이후로 엄청난 반향을 일으켰습니다.

CycleGAN에 대해 알기 위해선 먼저 GAN과 Pix2Pix에 대해 알아야 합니다.

<br>

# GAN

GAN(Generative Adversarial Network)은 G(Generator)와 D(Discriminator) 두 개의 네트워크가 경쟁적으로 학습하면서 더 나은 결과물을 만드는 구조입니다.

아래 GAN 구조 그림의 G의 목표는 입력으로 들어온 흑백이미지에 대해 컬러 이미지를 만드는 것이 목표입니다. 반면, D의 목표는 입력으로 들어온 컬러 이미지에대해 해당 이미지가 진짜인지 아닌지 판별해 내는 것이 목표입니다.

G의 입장에선 fixed한 loss 함수가 아니라 moving하는 loss가 목표라 학습이 굉장히 어렵습니다.

특히나 model collapse라고 해서 G가 하나의 결과물만 내어버리는 현상이 빈번히 발생해 악명이 높습니다.

또한 어느 한쪽의 성능이 압도적으로 우월해 버리면 학습 자체가 안되는 현상도 자주 생겨 이를 학습하기가 굉장히 어려웠으나, DCGAN 논문에서 GAN을 학습하는 방법에 대한 기준점을 제시함으로써 이에대한 문제가 어느정도는 해소 되었습니다.

> GAN의 구조

![](https://user-images.githubusercontent.com/31475037/64156136-29d49480-ce6f-11e9-9e90-794b0360bcf1.PNG)

이러한 GAN의 loss 함수는 다음과 같이 정의 됩니다.

> G가 D를 속이기 위해 발전

![](https://user-images.githubusercontent.com/31475037/64162786-f0098b00-ce7a-11e9-8754-bae227005486.PNG)

> D는 이에 맞춰 G가 만들어낸것을 맞추기 위해 발전

![](https://user-images.githubusercontent.com/31475037/64162787-f0098b00-ce7a-11e9-937e-baa880c5f672.PNG)

이러한 GAN loss(Adversarial loss)를 이용해 GAN을 학습합니다.

<br>

# Pix2Pix

[Pix2Pix](https://arxiv.org/abs/1611.07004)는 Supervised learning의 한 종류로 들어온 이미지에 대해 target 이미지로의 변환을 시켜주는 모델입니다. 

논문에 실린 결과에서 보듯이, 굉장히 다양한 task에서 적용이 가능합니다.

> 입력으로 들어온 이미지를 목표로 하는 이미지로 변환시켜준다.

![](https://user-images.githubusercontent.com/31475037/64163442-3b706900-ce7c-11e9-9dd1-57e1b454426e.PNG)

기본적인 Pix2Pix의 loss는 굉장히 간단하며, L1 loss를 썼습니다.

다음 예시에서 보듯이, 입력으로 들어온 sementic segmentation 맵을 photo-realistic한 이미지로 바꿔주는데, 문제는 학습에 대한 결과물이 그닥 좋지 않습니다.

> L1 loss only

<center><img src="https://user-images.githubusercontent.com/31475037/64163937-23e5b000-ce7d-11e9-9e44-5e593307f030.PNG"></center>
L1 loss만을 이용해 학습을 하게 됬을 때의 문제는 각 픽셀 별로 완벽한 답을 찾기 보다는 중간값(안전한) 값으로 대충 얼버무리는 것이 loss를 줄이는데 더 도움이 되 이러한 blur한 이미지가 생성이 되게 합니다.

이러한 문제점으로 인해, 생성된 이미지는 photo-realistic하지 않습니다.

그런데 여기에 GAN을 사용하게 되면, 이미지가 blur가 아닌 꽤나 sharp하고 디테일하게 나옵니다.

왜냐하면 GAN은 G가 실제와 최대한 비슷한 데이터를 만들어내 D를 속이는 것이 목표이기 때문에 이러한 결과가 생기게 됩니다. 

특히나 L2 loss(MSE)를 이미지의 생성 모델에 사용했을 시 다음과 같이 blur가 된 이미지가 생성이 되는 경향이 있습니다. 반면 GAN을 사용한 모델은 디테일이 살아 있습니다.

L1 loss는 L2 loss보다는 blur가 덜하지만, 여전히 blur 문제가 남아 있습니다.

> GAN loss와 MSE 로스를 썼을 때의 차이, 순서대로 GT, L2 loss, GAN loss 별 결과물

<center><img src="https://user-images.githubusercontent.com/31475037/64164944-074a7780-ce7f-11e9-92ff-1fddf27fd9b0.PNG"></center>
특히나 결과물로 생성된 이미지의 디테일이 살아 있다는 것을 논문에선 입증해 보였습니다.

(좀더 photo-realistic함)

> L1 loss and GAN loss

<center><img src="https://user-images.githubusercontent.com/31475037/64163939-247e4680-ce7d-11e9-9bfc-71041ec00def.PNG"></center>
논문에서 GAN loss 뿐만 아니라 L1 loss도 같이 써준 이유는, 모델 학습의 안정성에 있어 L1 loss가 큰 영향을 준다고 말합니다.

또한 D에는 patchGAN을 사용해 전체 이미지를 D에 넣는것이 아닌, 이미지의 일부분만 넣어서 학습을 하는 방식을 선택해 성능을 높였습니다. 

<br>

# CycleGAN

[CycleGAN](https://junyanz.github.io/CycleGAN/)은 기존 Pix2Pix 연구의 연장선으로 시작되었습니다.

기존의 Pix2Pix가 가지고 있던 문제인 학습을 위해선 labeling이된 대규모의 데이터셋이 필요하다는 문제를 해결하기 위해 4개의 네트워크를 학습시키는 모델을 구성했습니다.

Generator는 G, 도메인 X, 도메인 Y 라고 표현했을 때 G가 X 도메인에 속해있는 이미지에서 Y도메인에 속해있는 이미지로 Image Translation을 한다는 것을 다음과 같이 표현합니다.

**G : X -> Y**

먼저 G 하나만을 사용한다면 어떤 결과가 나올까요? 

그렇게 되면 G가 입력으로 들어온 이미지에 대해서는 무시를 해버리고, 목표 이미지와 비슷하게만 만들고, D가 이에 대해 적응을 해버린다는 문제가 생깁니다.

> G 하나만을 사용

![](https://user-images.githubusercontent.com/31475037/64165741-9c01a500-ce80-11e9-9cfc-069251531689.PNG)

이러한 문제를 해결하기 위해 논문에서는 또 다른 Generator F를 사용합니다.

**F: Y -> X**

이렇게 X 도메인에 속해있는 이미지는 G를 통해 Y 도메인에 갔다가, 다시 F를 통해 X도메인으로 돌아오는 Cyclic한 구조를 이루게 됩니다.

이렇게 Cyclic하게 모델을 구성하게 되면 G가 입력 이미지를 무시하는 문제는 해결이 됩니다.

> Reconstruc x

![](https://user-images.githubusercontent.com/31475037/64165740-9c01a500-ce80-11e9-9673-b159320d9c89.PNG)

이를 수식과 그림으로 표현하면 다음과 같습니다.

> X -> Y -> X 인 경우



![](https://user-images.githubusercontent.com/31475037/64226907-4084f500-cf1c-11e9-91cc-33bf65ffcb66.PNG)



그런데 CycleGAN 같은 경우는 도메인이 X, Y 두가지 이기에 Y 도메인에 대해서도 Cyclic한 변환을 해줍니다.

> Y -> X -> Y인 경우

![](https://user-images.githubusercontent.com/31475037/64226908-4084f500-cf1c-11e9-9ee5-f24e5c19156b.PNG)



제시된 두 경우를 종합해서 loss로 표현하면 다음과 같습니다.

> Loss 

![](https://user-images.githubusercontent.com/31475037/64165739-9c01a500-ce80-11e9-9f4f-fa701ea6486a.PNG)



<br>

## Training

### Geneartor

G의 구조로 여러 가지 구조를 실험해 보았다는데, 대표적으로 2가지를 언급했습니다.

첫번째는 Encoder-Decoder 구조인 UNet 구조입니다. UNet의 구조는 극단적인 bottleneck 구조입니다.

이러한 구조의 장점은 모양의 변화가 가능하지만, 모델의 학습 자체가 굉장히 불안정 합니다.

또한 입력과 목표 데이터가 비슷할 경우 Skip Cunnection의 사용이 늘어나 모델의 depth 적인 측면으로 학습이 전혀 되지 않는다고 말합니다.

특징으로는 UNet을 이용해 학습을 할 시 결과 이미지에서의 모양에 대한 변화가 크다고 합니다.

> Encoder-Decoder(UNet) 계열

<center><img src="https://user-images.githubusercontent.com/31475037/64169935-d7ed3800-ce89-11e9-849b-d09213496360.PNG"></center>
두번째로 논문에서 사용한 모델인 ResNet 입니다.

ResNet은 UNet에 비해 더 적은 parameter를 가지지만 ill-posed problem에 있어 더 좋은 결과를 냅니다.

특히나 안정적인 학습이 가능합니다.

특징으로는 ResNet을 이용해 학습을 할 시 결과 이미지에서의 모양에 대한 변화가 거의 없다고 합니다.

> ResNet

<center><img src="https://user-images.githubusercontent.com/31475037/64169937-d7ed3800-ce89-11e9-8fa7-eba0d686be86.PNG"></center>
### Loss

기본적인 GAN Loss는 cross-entorpy loss 기반입니다.

하지만 해당 loss의 단점은 vanishing gradient 문제가 생긴다는 것입니다.

그렇기에 대안으로 제안된 LSGAN은 MSE처럼 loss를 measure해 이 문제를 해결했습니다.

> GAN Loss의 종류

<center><img src="https://user-images.githubusercontent.com/31475037/64166533-41694880-ce82-11e9-8d51-4f9b5b92864d.PNG"></center>
또 GAN loss와 함께 L1 loss도 함께 사용하여 모델의 안정성을 향상시켰습니다.



### Identity mapping

identity mapping은 입력을 넣었을 때 G가 입력을 내놓게 하는 loss로 저자의 말로는 학습시 대다수의 경우에는 사용을 하지 않았다고 했지만, 입력과 목표 데이터셋이 비슷할 경우 굉장히 효과적이었다고 말합니다.

즉, G : X -> Y 일 경우, 도메인 X와 Y의 특성이 비슷할 경우 identity mapping의 효과가 좋습니다.

> identity mapping 결과 비교

<center><img src="https://user-images.githubusercontent.com/31475037/64166535-4201df00-ce82-11e9-9279-77b89287a213.PNG"></center>
### replay buffer

GAN을 학습하다보면 다음과 같이 학습이 굉장히 불안정 합니다.



![](https://user-images.githubusercontent.com/31475037/64168000-95c1f780-ce85-11e9-972f-77ecacfefc92.PNG)

이러한 불안정 문제를 해결하기 위해 크게 두가지 방법이 존재하는데

첫번째로는 D를 여러개 사용해 D의 결과물의 평균을 내어 학습을 안정적이게 만드는 것입니다.

하지만 이 방법은 전체 모델 크기가 너무나도 커져, 이미 4개의 모델을 쓰고 있는 CycleGAN에는 적절치 않았다고 판단했습니다.

두번째로는 예전 G가 생성했던 결과물을 다시 넣어줘 학습을 시키는 것입니다.

이 방식이 replay buffer 방식이고, 해당 방식을 사용해 굉장히 안정적인 학습이 가능했다고 합니다.

<br>

## Results

논문에 실린 결과물은 굉장히 좋은것을 볼 수 있습니다.

안타깝게도 실제 모델을 돌려보면 전체의 10% 정도만이 볼만한 수준이고 나머지 90%는 썩 좋지 않은 결과물이었습니다.

> 잘 생성된 결과물



![](https://user-images.githubusercontent.com/31475037/64166162-89d43680-ce81-11e9-98aa-3013f19749ae.PNG)



## Failure cases

ResNet을 사용한 CycleGAN 모델의 단점은 입력의 모양을 바꾸기가 쉽지 않다는 것입니다. 이러한 문제로 인해 사과를 오렌지로 바꾸는 task에 대해서 사과의 모양을 바꾸진 못하고, 색만 바꿔버린 결과물이 도출됩니다.

> 전체적인 모양을 바꾸지는 못함

![](https://user-images.githubusercontent.com/31475037/64166537-4201df00-ce82-11e9-8b9f-796fbf97103b.PNG)

또한 사람과 말을 제대로 구분하지 못해 아래와 같은 결과물을 내기도 합니다.

> 사람과 말을 제대로 구분을 하지 못함

![](https://user-images.githubusercontent.com/31475037/64166538-4201df00-ce82-11e9-9afd-80a8c839c538.PNG)



<br>

**참고 강의**

[CycleGAN 저자 특강](https://tv.naver.com/v/2203900)

[NIPS2016 GAN tutorial](https://arxiv.org/pdf/1701.00160.pdf)

[NVIDIA NCSOFT](https://www.youtube.com/watch?v=Ko31fYGT20Y)