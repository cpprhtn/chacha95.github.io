---
layout: post
title: SlowFast Networks 리뷰
tags: [Video Understanding]
---

이번 포스트에서는 CVPR2019 워크숍에서 열린 [AVA challenge](https://research.google.com/ava/)의 한 topic인 AVA challenge의 Action 분야에서 1등을 차지한 SlowFast Networks에 대해 알아보겠습니다. 또한 이 논문은 ICCV2019에 oral 발표 예정입니다.

 [이 논문](https://arxiv.org/pdf/1812.03982.pdf)은 FAIR 그룹에서 쓴 논문이며, 저자에 Kaiming He가 있습니다! 논문 내용도 굉장히 좋으며, 본인들이 설계한 모델이 왜 잘 되는지에 대해 사람의 시각 시스템을 모방하였기에 잘 만들었다고 설명하고 있습니다. 특히나 요즘 딥러닝 논문들이 왜 잘되는지 이유에 대한 설명이 조금 빈약하였는데, cognitive science 측면에서 설명한 점이 인상깊었습니다.

<br>

## AVA Challenge 2019

AVA는 human action에 대한 이해를 높이기 위해 비디오의 시청각 label을 제공하는 프로젝트입니다. Label이 달린 동영상은 모두 15분 길이의 movie clip입니다. 각 clip은 사람에의해 분류되었습니다.

이름에 있는 atomic이란 단어의 의미인 원자단위라는 의미처럼, 엄청난 숫자의 사람이 붙어 비디오를 최소한으로 쪼개 수작업으로 레이블링을 하였습니다.

데이터셋은 크게 2개로 구분되며 AVA Action dataset만 보겠습니다.

- AVA Action dataset

  AVA dataset은 430개의 15분짜리 movie clip에 80개의 시각 효과를 주석으로 표시합니다. action은 시간과 공간적으로 localized되어 인간 당 여러 개의 레이블이 달려있습니다. 

자세한 사항은 [AVA CVPR2018](https://arxiv.org/pdf/1705.08421.pdf) 논문을 읽어보시길 바랍니다.

<br>

## Introduction

논문에서는 video recognition에서 slow motion과 fast motion의 기여도가 다르다고 말합니다.

또한 이런 관점에서 spatial structures와 temporal events를 분리해서 봐야한다고 말합니다.

spatial semantics of visual content는 느리게 변합니다. 예를 들어 손을 흔드는 것은 손이라는 identity를 바꾸진 않습니다. 마찬가지로 사람은 사람이 걷다가 뛰더라도 항상 사람입니다. (사람이라는 identity가 바뀌지 않는다는 의미) 

이런 categorical sementic(혹은 colors, textures, lighting..)을 인식하는 것은 상대적으로 느리게 인식됩니다. 

반면에 모션은 물체의 identity에 비해 상대적으로 빠르게 변화됩니다. 이 부분은 저자가 제안하는 부분에서 
빠르게 변화하는 frames을 통해 표현됩니다.

이런 직관을 통해 저자는 Two-pathway SlowFast 모델을 제안합니다.

Slow pathway는 semantic information을 포착합니다.

Fast pathway는 fast refreshing frame(high temporal rate)을 통해 빠르게 변하는 motion을 포착합니다. 

Fast pathway는 전체 계산량의 20%만 차지합니다. 이 pathway는 더 적은 channel과 더 적은 spatial information을 포착하도록 설계되었습니다.

두 pathway는 lateral connection(나중에 feature가 합쳐지는 방식)에의해 합쳐집니다.

논문에서는 기존의 optical flow와 RGB를 입력으로 같이 쓰는 two-stream network가 좋지 않다고 말합니다. optical flow는 hand-designed representation이고 two-stream methods는 end-to-end 방식으로 학습하지 않기 때문입니다. (일반적으로 flow와 RGB 네트워크를 따로 학습시킴)

> SlowFast Net

![](https://www.lyrn.ai/wp-content/uploads/2018/12/SlowFast-1024x495.png)

<br>

## Biological derive

저자의 연구는 retinal ganglion cells에 관한 연구에의해 영향을 받았습니다. 사람 시각 시스템의 cell들이 80%의 Parveocellular(P-cells)와 15~20%의 Magnocellular(M-cells)로 이뤄져 있다고 합니다.

M-cells은 high temporal frequency에 대한 연산을 하며, fast-temporal change에 반응합니다. 그러나 spatial detail이나 color에는 거의 반응을 하지 않습니다.

P-cells는 spaital detail, color에 대해 반응하지만, temporal한 정보에는 천천히 반응한다는 특징이 있습니다.

이런 연구 결과에 기반하여, 저자는 모델을 설계했으며 유사한 결과를 내었습니다.

특히나 주목할만한 점은 각 pathway의 계산량이 8:2(Slow : Fast), cells의 분포가 8:2(P-cells : M-cells)로 매우 유사하다는 것입니다. 어찌보면 인간의 시각 인지 시스템을 딥러닝 버전으로 만들어 적용했더니만 좋은 결과를 얻었다고도 볼수 있겠네요.

<br>

## Hyper Parameter

본 논문에서는 다양한 Hyper parameter를 설정, 여러 실험을 하였습니다. 본 논문에서 실험을 통해 확인하고 제안하는 Hyper Parameter는 다음과 같습니다.

- S = 영상을 정사각형으로 crop한 height 혹은 width 정보

- T = Temporal length= 4

- α = speed ratio = 8

- β = channel ratio = 1/8

- τ = temporal stride = 16

- 원본 비디오 클립은 총 T x τ = 64 frame입니다.
- Fast pathway에 들어가는 frame은 αT=32, Slow pathway에 들어가는 frame은 T=4입니다

## Slow Pathway

논문에서 쓴 τ = 16으로 원본 비디오 클립에서 16프레임 마다 샘플링을 한다는 의미입니다. 이는 30fps 영상에서 대략 1초에 2 frame만 추출한다는 의미입니다.

Slow pathway에 들어가는 샘플링된 frame 수가 T라고 하면, 원본 비디오의 총 프레임 수는 T x τ 입니다.

<br>

## Fast Pathway

**High frame rate**

speed ratio α=8

Fast path way에 들어가는 샘플링된 frame 수는 αT입니다.(slow pathway보다 α만큼 더 밀집되 있습니다.)

α 값이 이 연구의 핵심이며, pathway마다 서로 다른 temporal speed를 가지도록 설계를 합니다.

**High temporal resolution features**

저자는 temporal downsampling layer를 쓰지 않습니다(시간축으로 pooling을 하지 않는다는 의미)

하지만 classification layer 직전의 global pooling 직전 layer에서는 풀링을 한번 해줍니다.

그렇기에 마지막 layer를 제외하고 feature는 αT frame을 가집니다.

**low channel capacity**

Fast pathway는 lower channel capacity를 가지기에 computation 양이 적어져 lightweight합니다.

채널의 수는 Slow pathway에 비해 β만큼 줄여줍니다.

저자의 실험에 의해 찾은 파라미터는 β = 1/8 입니다. 

이러한 low channel capacity는 spatial semantics를 표현하는 능력이 떨어집니다. 논문에서는 이를 일종의 trade-off라고 표현하고 있습니다.(temporal-spatial trade-off)

Fast pathway는 그것의 spatial modeling 능력을 약하게 하는 대신, temporal modeling 능력을 증가시켰습니다.

<br>

## Instantiations

논문에서 ResNet-50을 기반으로 제안한 모델은 다음과 같습니다.

> 예시 모델(ResNet-50)

<center><img src="https://user-images.githubusercontent.com/31475037/62919792-b0510580-bdde-11e9-8fc9-c6da4410f5b3.PNG"></center>
<br>

## Experiments

### Training

- Kinetics 데이터셋을 이용해 scratch부터 학습 시켰음(no pretrained)

- SGD 사용

- 하나의 비디오 클립에서 랜덤하게 T x τ 프레임 만큼 샘플링 합니다.

- Slow pathway 에는 T만큼의 프레임 입력

- Fast pathway에는 αT만큼의 프레임 입력

- [256, 320]로 resize 후 랜덤하게 224x224 crop



### action classification

action calssification task에서의 결과는 다음과 같습니다. 평가 데이터셋은 Kinetics-400을 사용했습니다.

아래 결과표에서와 같이 T값이 늘어나면 늘어날수록 accuracy는 증가하지만 그만큼 계산량이 증가하는 결과를 초래합니다. T가 8에서 16으로 2배 증가하자, 계산량이 2배이상 증가하는 것을 볼 수 있습니다.

> action classification results

<center><img src="https://user-images.githubusercontent.com/31475037/61861644-5065ee00-af07-11e9-9d19-3371565c4ff6.PNG"></center>
논문에서는 indivisual한 모델을 사용했을 때의 결과도 실험을 했습니다. 

> Indivisual pathway

![](https://user-images.githubusercontent.com/31475037/61862755-5957bf00-af09-11e9-83f6-da51f8a7f392.PNG)

외에도 channel ratio값의 변화에 따른 계산량 차이와 accuracy에 대한 연구도 했습니다.

> β값의 변화에 따른 결과

<center><img src="https://user-images.githubusercontent.com/31475037/61861642-5065ee00-af07-11e9-8a29-d94907d5c907.PNG"></center>
가장 흥미로웠던 것은 기존 논문들에서 입력 영상의 spatial size가 224x224 pixel 혹은 112x112 pixel 둘중 하나였는데 둘 중 어느 것이 더 효과적인지 실험을 했습니다. (이 외에도 optical flow나 gray-scale도 실험)

> 여러 조건에 따른 accuracy 측정

<center><img src="https://user-images.githubusercontent.com/31475037/61862919-92902f00-af09-11e9-9256-5ad4a2a1c7b2.PNG"></center>
<br>

## Conclusion

본 논문에선 이전엔 없었던 새로운 Two-Stream Net을 제안하였고, 모델을 사람의 인지 시스템을 모방해 만들었습니다. 또한 이전 다른 논문들에선 자세히 다루지 않았었던 fps나 입력 총 프레임 수와 같은 부분에 대해서도 굉장히 다양하고 디테일한 실험을 하고, 이를 표로 간단히 나타냈습니다.

논문의 내용이 깊고 복잡해서 이해하는데 오래 걸렸지만, 성능 자체가 accuracy 측면이나, computation 측면이나 정말 엄청난 발전을 이뤘기에 앞으로의 action classification 분야의 backbone이 될 연구라고 생각됩니다.

<br>

**참고자료**

[SlowFast](https://www.lyrn.ai/2018/12/21/slowfast-dual-mode-cnn-for-video-understanding/)

<br>

