---
layout: post
title: Basic of Action Recognition 리뷰
tags: [Video Understanding]
---

이 포스트는 [Qure.ai Blog](http://blog.qure.ai/notes/deep-learning-for-videos-action-recognition-review)글을 일부 번역했습니다.

video understanding task의 대표적인 task가 action recognition입니다.

action recognition에서 비디오의 전체 프레임에서 서로 다른 동작을 구별해내는 task 입니다. action은 전체 프레임 동안 이어질수도, 아닐수도 있습니다.

이러한 특징때문에 image classification task의 자연스런 연장선이라고 보는 견해도 있습니다. 하지만 이미지 분류 (ImageNet)의 딥러닝 적용이 성공 했음에도 불구하고 action recognition task의 발전은 더 느려졌습니다.

이 작업을 어렵게 만든 이유는 다음과 같습니다.

- 계산량이 너무 많음

  간단한 한장의 이미지를 101개의 클래스로 분류하는 CNN은 5M개의 파라미터를 가지고 있는 반면, 같은 구조의 3D 형태(video에 맞는)의 CNN은 33M개의 파라미터를 가집니다. 3D ConvNet 구조를 이용해 UCF101 데이터를 이용해 학습을 하면 3~4일이 걸리고, Sports-1M 데이터를 이용하면 학습에 두달 정도 걸립니다. 

- capturing long context

  action recognition은 연속적인 프레임 속에서 spatiotemporal(시공간) context를 포함해야 합니다. 추가적으로, spatial한 정보의 포착은 카메라의 움직임에 따라 변합니다. 또한 local context 뿐만 아니라 global한 context 또한 중요합니다. 이는 action에대한 robust한 예측의 포착이 필요하다는 의미입니다.

  > 자유형(좌), 평형(우)

  예시의 데이터셋은 다음과 같습니다.

  왼쪽 비디오에서 중간에 사람을 비추는 카메라의 앵글이 바뀔 수도 있습니다. 이로인해 spatial한 정보의 포착이 바뀌는 경우가 생깁니다.

  잘 훈련된 classifer는 자유형과 평형의 동작이 다름에도 불구하고 temporal한 정보를 잘 수집해 수영이라는 class로 분류합니다.

  

  <center><figure class="second">
  	<img src="http://blog.qure.ai/assets/images/actionrec/fronststroke.gif" width="250">
  	<img src="http://blog.qure.ai/assets/images/actionrec/breaststroke.gif"
  width="250">
  </figure></center>

  ​                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                      

<br>

## Approach 1 : Single Stream Net

논문이 나온 시기(2014)에는 다양하고 크기가 큰 benchmark dataset에 존재하지 않아서, Sports-1M 데이터셋을 만들었습니다.(기존에 UCF-101이 존재하긴 했음)

해당 논문에서는 다양한 방식의 temporal information의 fusion에대한 실험을 소개하고 있습니다.

훈련에 사용된 데이터셋은 1M이고, 487개의 카테고리(클래스)를 가집니다.

> Fusion method

모든 방법에는 입력으로 연속적인 비디오 프레임이 들어갑니다.

**Single Frame** 방식은 프레임에서 온 정보들을 마지막에 fuse하는 구조입니다.

**Late Fusion** 방식은 파라미터를 공유한느 두개의 네트워크를 사용하며, 15 프레임 떨어져 있습니다. 또한 마지막에 fuse를 합니다.

**Early Fusion** 방식은 첫번째 레이어가 10개의 프레임을 convolving하는 구조입니다.

**Slow Fusion** 방식은 early fusion과 late fusion 방식의 밸런스를 맞춘 구조로,  최종 prediction을 위해서 여러 프레임들이 sampling되는 방식입니다.

<center><img src="http://blog.qure.ai/assets/images/actionrec/Karpathy_fusion.jpg"></center>
이러한 다양한 실험에도 불구하고, 좋지않은 결과물에 대해 저자는 다음과 같은 실패의 이유를 말합니다.

- 학습된 spatiotemporal features가 motion features를 포착하지 못함
- 데이터셋이 다양하지않고, 디테일한 학습하기에 부족함

<br>

## Approach2 : Two Stream Net

논문 저자는 기존 approach1에서의 실패원인에대해 분석하고 이를 타개할 해결책을 제안했습니다.

딥러닝 아키텍쳐가 temporal feature를 잘 포착하지 못한다는 부분을 stacked optical flow vector를 이용하여 극복하였습니다.

spatial context에 대해 분석하는 single net(pretrained)과 temporal context에 대해 분석하는 네트워크로 분해하였습니다.

Spatial stream Convnet은 single frame가 입력으로 들어갑니다.

Temporal stream Convnet은 optical flow가 입력으로 들어갑니다.

두개의 다른 stream은 각각 학습되고, SVM을 통해 합쳐집니다.( SVM이 classifier 역할)

최종 prediction은 approach1에서와 동일하게 샘플 프레임들의 평균값으로 prediction합니다.

> Two Stream architecture

![](http://blog.qure.ai/assets/images/actionrec/2stream_high.png)

일반적으로 대다수의 two stream method가 one stream method보다 성능이 높게 나옵니다.

하지만 이 방식도 다음과 같은 문제점이 있습니다.

- 비디오의 long range temporal information 학습의 부재
- 학습 클립이 비디오로 부터 샘플링 되는 방식인지라 잘못된 labeling 문제가 발생합니다.

<br>

## Approach 1 기반 아키텍쳐

리뷰할 아키텍쳐들은 전부 Approach1에 기반한 아키텍쳐 입니다.(Single Stream)

### LRCN

~~~
Long-term Recurrent Convolutional Networks for Visual Recognition and Description
Submitted on 17 November 2014
~~~

CNN을 encoder로 LSTM을 decoder로 사용하는 구조를 제안했습니다.

input으로 RGB 이미지만 넣었을 때와 RGB를 넣었을때의 결과값과 optical flow를 넣었을 때의 결과값을 weighted score 방식을 사용했을 때를 비교 분석했습니다.

> LRCN 아키텍쳐

<center><img src="http://blog.qure.ai/assets/images/actionrec/GenericLRCN_high.png"></center>
학습 방법으로는 비디오로부터 16프레임의 클립을 샘플링합니다. 아키텍쳐는 end-to-end 방식이며, 입력 영상은 RGB이거나 optical flow 입니다.

최종 prediction은 한 클립의 각 time step의 평균 예측값으로 결정됩니다.

| Score | Comment                                       |
| ----- | --------------------------------------------- |
| 82.92 | Weighted score of optical flow and RGB inputs |
| 71.1  | Socre with just RGB                           |

주요 Contributions

- RNN 기반(LSTM)의 아키텍쳐 제안
- video respresentation에서 encoder-decoder 구조 제안
- action recognition task에서 end-to-end 학습 방식 제안

하지만 이 방식에는 몇가지 결점이 존재합니다.

- 샘플링된 비디오 클립의 라벨링이 맞지 않을 수 있습니다.
- long range temporal information 포착이 힘듭니다.

<br>

### 3D-ConvNet(C3D)

```
Learning Spatiotemporal Features with 3D Convolutional Networks
Submitted on 2D December 2014
```

기존에 2D convoltuion이 spatiotemporal 정보에 대해 잘 파악하지 못한다는 특성을 보완하기 위해 제안된 3D convolution 구조입니다.

다음과 같이 2D convolution은 output이 2D이지만, C3D는 ouput이 3D(volume) 형태로 나옵니다.

해당 논문에서 사용된 input dimension은 3 x 16 x 128 x 171 입니다.

C3D는 video 분석을 위한 feature extractor로서 훌륭한 역할을 수행합니다.



![](https://jaydottechdotblog.files.wordpress.com/2017/01/c3d-2d-3d-convolutions-diagram.png?w=730)



| Score | Comment                        |
| ----- | ------------------------------ |
| 82.3  | C3D(1 net) + linear SVM        |
| 85.2  | C3D(3 nets) + linear SVM       |
| 90.4  | C3D(3 nets) + iDT + linear SVM |



> 3D convolution이 spatiotempporal 큐브에 작동하는 모습

<center><img src="http://blog.qure.ai/assets/images//actionrec/trial.gif" width="70%"></center>
> 2x2x2 C3D가 작동하는 모습, 결과물이 volume의 형태로 나온다

<center><img src="https://github.com/OValery16/Tutorial-about-3D-convolutional-network/raw/master/images/3dconv.gif"></center>
주요 contribution

- feature extractor로서 3D Conv를 제안
- 3D Conv의 커널 사이즈와 아키텍쳐에 대한 전반적인 조사
- deconvolution layer를 모델 결정에 대한 해석으로 사용




long range temporal information에 대한 문제가 여전히 남아있습니다.

게다가 C3D의 계산량 문제가 여전히 큰 문제로 지적됩니다.

<br>

**참고 강의**

[Deep Learning for Videos: A 2018 Guide to Action Recognition](http://blog.qure.ai/notes/deep-learning-for-videos-action-recognition-review)

[awesome-action-recognition](https://github.com/jinwchoi/awesome-action-recognition)

[video description review](https://arxiv.org/pdf/1806.00186.pdf)

[3D Conv tutorials](https://github.com/OValery16/Tutorial-about-3D-convolutional-network)

<br>

