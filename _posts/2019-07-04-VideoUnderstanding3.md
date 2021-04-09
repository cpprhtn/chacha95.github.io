---
layout: post
title: I3D(Inflated 3D ConvNet) 리뷰
tags: [Video Understanding]
---

[Quo Vadis, Action Recognition? A New Model and the Kinetics Dataset(CVPR 2017)](https://arxiv.org/abs/1705.07750) 논문은 action classification 분야의 backbone이 되는 중 하나입니다.

해당 논문에서는 action classification을 위한 새로운 데이터셋인 Kinetics 데이터셋을 만들고 이를 기반으로 I3D를 학습 시켰습니다.

논문에서는 I3D의 방법과 기존의 방법들에 대해 소개하면서 장단점을 비교 했습니다.

<br>

## Action Classification Architecture

> 논문에서 비교 실험한 구조

![](http://blog.qure.ai/assets/images/actionrec/recurrent_theme_high.png)

###The old 1 : ConvNet + LSTM

Image classification에서 성공했던 방법론을 다시 사용했습니다. 다만 sequntal한 video 데이터셋의 특성을 고려해 RNN계열인 LSTM 네트워크를 추가하였습니다.

CNN을 이용해 프레임마다 feature를 추출하고, 추출된 feature vector를 Decoder인 LSTM에 넣어주는 구조입니다. 논문에선 spirit of bag of words를 이미지 task에서 쓰였다고 말하고 있습니다. 

하지만 이러한 구조가 temporal structure를 무시하는 구조라 주장합니다. 특히나 high-level variation하게 모델링을 할 수는 있지만, fine한 low-level motion을 포착하기가 힘들다고 합니다. 또한 network를 여러 frame을 시간축으로 backpropagation하면서 unrolling하는 구조여서 학습이 expensive하다고 지적합니다.

논문에서는 기존 구조에서 BN(Batch Normalization)을 추가해 실험에 사용했습니다.

입력 비디오는 25fps 영상에서 1/5만큼 subsampling합니다. (사실상 5fps로 영상을 만든다는 의미)

### The old 2 : 3D ConvNets

3D ConvNets은 video modeling에서 가장 자연스러운 선택처럼 보입니다. 3D Conv를 이용해 spatiotemporal 정보를 잘 취득할 수 있습니다.

하지만 한 차원이 늘어난 만큼 학습해야할 파라미터가 너무 많아 학습이 힘들다는 단점이 있습니다.

3D Conv를 사용하면 최소한의 성능은 보장되지만, State-of-the-art 기술들에 비하면 부족합니다.

해당 논문에서는 입력 영상을 16-frame, 112x112 pixel 영상으로 만들어 넣어줬습니다.

### The old 3 : Two-Stream Net

기존의 모델들이 fine한 low-level motion을 포착하기 힘들다는 점을 극복하기 위해서 입력으로 RGB 프레임과 optical flow 프레임을 같이 넣어주는 구조입니다. 이러한 low-level motion정보를 같이 넣어줌으로서, RGB 만을 넣어주는 경우보다 거의 모든 경우에서 더 높은 성능을 보였습니다.

입력 영상으로는 10프레임의 비디오에서 5개의 연속적인 프레임을 샘플링해 넣어줍니다.

<br>

### Two-Stream Inflated 3D ConvNet

저자가 제안하는 방법은 2D ConvNet의 ImageNet 데이터셋으로 classification 문제를 푼 모델을 이용해 문제를 푸는 방식입니다.

**Inflating 2D ConvNets into 3D**

ImageNet 데이터를 이용해 2d classification 문제에 대해 학습시킨 2D ConvNet을 3D ConvNet으로 바꾸는 방법입니다.

저자는 이러한 N x N filters를 N x N x N 필터로 바꾸는 방식을 **inflating**이라고 불렀습니다.

디테일한 방식으로는 필터의 dimension을 늘려 준 뒤, weight를 1/N로 나눠줍니다.

> inflating 예시

![](https://user-images.githubusercontent.com/31475037/61213594-6e319700-a740-11e9-8730-7d4ee96b92fa.PNG)

### Two 3D Streams

본 논문에서는 optical flow 정보를 넣어줌으로써 motion 정보에 대해 더 잘 예측할 수 있도록 하였습니다.

두개의 I3D 모델을 각각 입력을 RGB, optical flow를 넣어주어 학습을 시키고, precition의 평균값을 취해 최종 prediction을 했습니다.

> I3D model

<center><img src="https://user-images.githubusercontent.com/31475037/61274906-b1931080-a7e7-11e9-9756-1542abc9758f.PNG"></center>
> Inc. module

<center><img src="https://user-images.githubusercontent.com/31475037/61275666-637f0c80-a7e9-11e9-817b-3543c5fd328c.PNG"></center>
### Training Detail

- ImageNet pretrained Inception-V1 사용
- 모든 conv layer에 BN(Batch Norm), ReLU사용
- SGD+momentum 0.9 사용
- 입력 영상은 256pixel로 resize 후 랜덤하게 224 pixel로 cropping을 함
- random하게 left-right flipping 진행
- 입력 영상은 25fps이며 총 길이는 64 frame

<br>

## Results

> parameter 수와 input size

![](https://user-images.githubusercontent.com/31475037/61274905-b1931080-a7e7-11e9-91d4-6be1ab7a83fd.PNG)



논문의 test에 따르면 RGB 하나만 넣어준 경우보단 RGB+Flow인 경우에서 항상 더 좋은 성능이 나왔습니다.

> Results

![](https://user-images.githubusercontent.com/31475037/61277464-2b79c880-a7ed-11e9-9ff3-386a7535919c.PNG)



또 논문에선 scratch부터 학습한 경우와 ImageNet pretrained를 사용한 모델의 성능 차를 보여줌으로써 ImageNet pretrained된 모델의 사용이 당위성을 보였습니다.

> pretrained에 따른 차이
>
> 굵은 글씨는 top-1 error, 괄호 안 숫자는 top-5 error

![](https://user-images.githubusercontent.com/31475037/61277593-7693db80-a7ed-11e9-872b-f9dbe429f31b.PNG)



<br>

**참고 강의**

[awesome-action-recognition](https://github.com/jinwchoi/awesome-action-recognition)

<br>

