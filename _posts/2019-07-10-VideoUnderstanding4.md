---
layout: post
title: Pretrained C3D ResNet in action classification 리뷰
tags: [Video Understanding]
---

우리는 2D 이미지 영역에서 pretrained 모델을 이용해 이미지의 feature들을 뽑을 때, ImageNet 데이터셋을 이용해 학습시킨 VGG 모델을 많이들 씁니다. 

2D 이미지 영역에서 그러하듯이, time 축이 추가되어 spatio temporal 정보를 가진 video 데이터에서도 이런 pretrained 모델을 만들 수 있을까요?

[Can Spatiotemporal 3D CNNs Retrace the History of 2D CNNs and ImageNet?(CVPR 2018)](https://arxiv.org/pdf/1711.09577.pdf) 논문에서는 "video 영역에서도 광범위하게 쓰일 수 있는 pretrained 모델을 만들수 있는가?" 에 대해 연구를 했습니다. 해당 논문은 네트워크가 매우 deep한 3D CNN에 관한 첫 연구입니다.(해당 논문에선 네트워크 깊이가 얇으면 shallow, 깊으면 deep이란 표현을 썼음)

저자는 video 데이터에서 ImageNet처럼 레이블링이 된 데이터 셋이 너무 적다고 말합니다. 다행히도 최근 action classification task 영역에서 레이블링이 된 데이터셋이 늘어나고 있으며, 그 중 Kinetics 데이터셋이 de facto(업계 표준)이 되고 있다고 주장하고 있습니다. Kinetics 데이터셋은 논문이 나올 당시 300K개의 데이터로 이루어질 만큼 대규모의 데이터셋이며, 현재는 700K까지 늘어났습니다. 

> 2D CNN의 image classification과 3D CNN의 action classification은 유사한 점이 많음

<center><img src="https://user-images.githubusercontent.com/31475037/61095281-c3924d80-a48d-11e9-9332-4e9157655c57.PNG"></center>
저자는 최근 video 관련 task에서 video의 spatiotemporal한 정보를 고려해, 2D convolution 대신 3D convolution을 많이들 사용하고 있다고 합니다. 하지만 데이터셋의 크기가 작아, ImageNet으로 pretrained된 2D CNN과 optical flow의 조합으로 만들어진 Two-stream 네트워크 구조에 밀린다고 지적하고 있습니다.

이러한 문제점을 타파하기 위해 3D convolution 기반의 pretrained ResNet을 Kinetics 데이터셋으로 학습시켜 사용해야 이러한 문제점들을 해결할 수 있다고 주장하고 있습니다.

논문에서는 ResNet을 baseline으로 다양한 모델에 대해 실험을 했습니다. 

ImageNet의 경우와 마찬가지로 네트워크가 깊어지면 깊어질수록 더 좋은 성능을 냈습니다.

> 깊이에 따른 네트워크 성능 측정

<center><img src="https://user-images.githubusercontent.com/31475037/61095283-c3924d80-a48d-11e9-89ca-dc73576855a9.PNG"></center>
또한 저자는 ResNet을 기반으로 여러 모델을 실험하였습니다. (모델에 대한 자세한 내용은 논문 참조 바람)

> 다양한 구조의 ResNet 성능 측정

![](https://user-images.githubusercontent.com/31475037/61095284-c42ae400-a48d-11e9-9c0a-2b75d3363f3c.PNG)



<br>

## Training Method

학습 방식은 다음과 같습니다

- Stochastic gradient descent with momentum 방식 
- 입력 영상으로 16 프레임의 영상이 들어감
- 입력 영상의 크기는 112 pixel
- 입력 영상의 size : 3 channel x 16 frames x 112 pixels x 112 pixels
- weight decay 0.001, momentum 0.9 사용
- 논문에는 batch size가 나와있지 않지만 official code에서는 batch size가 128임

> Top-1 accuracies(Kinetics validation set)

<center><img src="https://user-images.githubusercontent.com/31475037/61095280-c3924d80-a48d-11e9-863a-3463b0af6d8c.PNG"></center>
이 논문의 주요 contribution인 과연 pretrained 모델을 이용해 fine tunning 작업을 진행하면 더 높은 정확도를 얻을 수 있는가에 대한 부분입니다.

저자는 Kinetics 데이터셋에 대해 학습을 한 후, preatrained된 네트워크의 conv5 layer와 fully cunnected 레이어만 fine tunning을 진행했습니다. (이 방식이 finetunning의 성능을 극대화 시켰다고 함)

ResNet-18를 preatrained없이 UCF-101 데이터셋만을 이용해 학습을 하면 Top-1 accuracy가 42.4%밖에 안나오지만, pretrained 모델을 이용해 finetunning을 진행하면 accuracy가 84.4%까지 증가한 것을 볼 수 있습니다.

> pretrianed된 모델을 이용해 fine-tunning

<center><img src="https://user-images.githubusercontent.com/31475037/61111235-14249d80-a4c4-11e9-9789-90f4ad2d9235.PNG"></center>
> 다른 기법들과 비교

저자의 여러 실험중 가장 좋은 모델은 입력 영상이 64프레임이고 모델은 ResNeXt-101 모델이였습니다. Two-straem I3D보다는 성능이 떨어졌지만, 굉장히 좋은 성능을 보였습니다.

<center><img src="https://user-images.githubusercontent.com/31475037/61110658-b17ed200-a4c2-11e9-9c2e-bfb767f9ada0.PNG"></center>
<br>

## Conclusion

- ResNet-18의 학습은 UCF-101, HMDB-51, ActivityNet 데이터셋에는 overfitting 되었으나, Kinetics 데이터셋에는 overfitting 되지 않았습니다.
- Kinetics 데이터셋은 deep 3D CNN을 학습하기에 충분한 크기의 데이터셋이며, 152 ResNet Layer까지 학습 가능하다고 합니다.
- Kinetics 데이터로 pretrained한 3D CNN을 UCF-101이나 HMDB-51 데이터셋으로 finetunning하면 더 좋은 성능을 얻었습니다.

해당 논문을 통해 3D CNN task에서도 pretrained 모델을 이용해 적은 데이터셋만으로도 높은 성능을 얻을 수 있는 방법론을 알게되었습니다!



<br>

**참고 강의**

[awesome-action-recognition](https://github.com/jinwchoi/awesome-action-recognition)

[3D ResNets for Action Recognition](https://github.com/kenshohara/3D-ResNets-PyTorch)

[Can Spatiotemporal 3D CNNs Retrace the History of 2D CNNs and ImageNet?](http://openaccess.thecvf.com/content_cvpr_2018/papers/Hara_Can_Spatiotemporal_3D_CVPR_2018_paper.pdf)



<br>

