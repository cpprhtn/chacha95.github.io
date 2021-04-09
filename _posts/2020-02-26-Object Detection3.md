---
layout: post
title: 1-Stage detector 리뷰
tags: [Object Detection]
use_math: true
---

# 1-Stage detector 리뷰

기존에 존재하던 R-CNN 계열의 기술들은 2-stage detector 였습니다. 2-stage detector는 객체를 검출하는 정확도 측면에선 굉장히 좋은 성능을 냈지만, 속도(FPS) 측면에서는 너무나도 느렸습니다. 실제 Real-time detection을 하기 위해선 보통 30fps정도가 필요한데 Faster R-CNN의 경우 5fps 밖에 안됬으니까요. 

이런 속도 문제를 해결하기 위해 region proposal과 classification을 동시에 하는 1-stage detector가 제안되었습니다.

> object detection에서 중요한 모델들

![](https://user-images.githubusercontent.com/31475037/75324222-d2612f80-58b9-11ea-8ae5-e1ad0e1ccfd5.png)

<br>

## YOLO(You Only Look Once)

YOLO는 2015년에 나온 논문으로 기존에 object detection에서 주류가 되던 R-CNN 계열의 기술의 단점인 FPS(Frame Per Second) 문제를 해결했습니다. 

[YOLO CVPR2015 발표 영상](https://www.youtube.com/watch?v=NM6lrxy0bxs&t=680s) 을 보시면 기존 기술들의 FPS 문제가 무엇이고 해당 문제를 해결하기 위해 저자가 제안한 YOLO에 대해 간략히 설명하고 있습니다. 

기존에 발표된 DPM(Deformable Parts Models)에서 사용된 sliding window나 R-CNN에서 사용된 selective search를 기반으로한 region proposal 방식 때문에 속도 문제가 있습니다. 이러한 방식들을 탈피해 grid라는 개념을 도입해 속도를 향상시켰습니다.



### Unified Detection

기존에 region proposal과 classification을 따로 하던것을 한번에 하는 방식입니다.

입력 이미지를 S x S grid로 나눕니다. 만약에 객체의 중심이 grid cell에 들어가면, 해당 grid cell은 그 객체를 탐지하는 역할을 합니다.

각 gird cell은 B개의 bbox를 예측합니다. 그리고 해당 bbox에 대한 confidence score를 예측합니다. Confidence score란 해당 bbox안에 물체가 있는지 없는지 확률값으로 나타낸 값입니다. 

> YOLO Unified Detection

![](https://www.dropbox.com/s/qf4vrq8udy8mcwe/Screenshot%202018-05-02%2014.33.00.png?raw=1)



### YOLO Pipeline

기본 네트워크 구조는 24개의 Conv layer와 2개의 FC layer로 이루어져 있습니다. 저자는 기본 구조 이외에도 Conv layer를 9개만 사용하는 Fast YOLO 구조도 제안해 성능을 극한으로 끌어올렸습니다.

> 전체 pipeline

![](https://miro.medium.com/max/1204/1*z5lLvL3r8MeOmgcGkYnT2A.png)



### YOLO 성능

YOLO는 물체를 탐지하는 성능은 많이 줄였지만 속도적인 측면에서 기존 기술들을 훨씬 뛰어넘는 결과를 가져왔습니다.

> Real-Time 성능을 보여줌

![](https://miro.medium.com/max/538/1*zxDddLucPHk2sM5DHHEWvw.png)



### YOLO의 한계

1. grid cell이 하나의 클래스만 예측 -> 작은 object가 하나의 cell에 여러개 있으면 예측이 힘듭니다. YOLO는 각 grid cell이 두개의 bbox만 예측하고, 하나의 클래스만 가지기에 이런 문제가 나타납니다.
2. Grid cell의 개수가 이미지당 98개 밖에 안되기에 bbox를 제대로 찾기가 힘든 문제가 존재합니다.
3. bbox 예측시, bbox regression을 하지 않고 다이렉트로 bbox를 예측하기에, bbox를 찾는 성능이 굉장히 떨어집니다.



<br>

## SSD(Single Shot MultiBox Detector)

SSD는 YOLO 이후에 나왔으며, 기존 YOLO가 가지고 있던 문제점인 검출 성능을 높이면서도 속도를 유지시켰습니다. 심지어 2-stage detector의 당시 최고 기술이던 Faster R-CNN 보다 더 좋은 검출 성능을 보였습니다.



### Default bbox

SSD 저자는 미리 정의된 anchor box처럼, 미리 정의된 default bbox를 구성했고, 논문에서도 anchor box와 매우 유사하다고 언급하고 있습니다. 각각 feature map마다 구현된 default box가 존재해, 해당 bbox를 이용해 객체를 검출합니다.



### SSD 구조

SSD의 구조는 크게 3 등분으로 나누어 볼 수 있습니다.

> SSD의 구조

![](https://user-images.githubusercontent.com/31475037/75512672-820edc80-5a35-11ea-8ad2-08d7415eb2df.png)

1. SSD는 VGG-16을 backbone으로 사용했습니다. 전체 네트워크를 다 사용한 것은 아니고 Conv5_3 layer 까지만 사용해서 feature를 추출했습니다. 

2. 추출된 feature는 Auxiliary detector(보조 검출기)들에 의해 정보들이 추출되고 추출된 정보를 직접적으로 사용해 bbox regression을 하게 되는데, 이러한 multi-resolution 구조를 사용하면 scale-invarint해진다고 합니다.

   아래 그림은 논문에서 든 예시인데요, 8x8 크기의 feature map 에서는 고양이와 같이 작은 물체를 검출하기 쉽지만, 4x4 크기의 feature map에서는 고양이와 같은 작은 물체를 검출하기 어렵다고 합니다. 그렇기에 큰 크기의 feature map에서는 작은 크기의 물체를, 작은 크기의 feature map에서는 큰 크기의 물체를 검출 하도록해서 scale-invarint하게 만들었다고 합니다.

   > multi resolution feature map이 다양한 크기의 물체를 검출 가능케 함

   ![](https://user-images.githubusercontent.com/31475037/75513158-48d76c00-5a37-11ea-8dbc-c24b7fd47a7a.png)

3. 마지막으로 bbox collections 부분은 Auxiliary detector를 통해 검출된 정보와 neural net을 feedforward해 나온 정보를 다 합쳐서 bbox regression 및 예측된 bbox에 있는 물체의 class에 대해 예측하게 됩니다.



요약하자면 SSD의 핵심 아이디어는 한개의 물체를 다양한 크기의 bbox를 이용해 multi-resolution 환경에서 예측하는 방법입니다.

> 한개의 물체를 다양한 크기의 bbox를 이용해 예측하려는 구조

![](https://user-images.githubusercontent.com/31475037/75513360-fc406080-5a37-11ea-844f-19adbb9702d1.png)



### Loss 함수

SSD의 loss 함수는 Faster R-CNN과 거의 동일하며 classification loss는 cross-entropy, localization loss(bbox regression)는 smooth L1 loss를 사용했습니다.  

> Faster R-CNN의 loss 함수와 같음

![](https://user-images.githubusercontent.com/24144491/48844092-4752d900-eddc-11e8-914b-71b33286b02c.png)



### Hard Negative Mining

Detection 문제의 학습 중에 가장 문제가 되는 점은, 학습 중에 모델이 예측한 bbox에 물체보다는 백그라운드(배경)가 많을 확률이 훨씬 높다는 문제가 있습니다. 해당 문제를 해결하기 위해서 저자는 bbox 안에 물체가 없는 negative bbox와 물체가 있는 positive bbox의 비율을 3:1로 맞추어 사용했습니다.



### Performence

SSD는 빠른 속도(59.8fps)와 함께 검출 성능 측면에 있어서도 당시 SOTA를 찍었으며, 추후에 나오는 1-stage detector들의 구조에 큰 영향을 주었습니다.

> SOTA 달성

![](https://junjiwon1031.github.io/assets/result_voc2007.png)



<br>

## RetinaNet

RetinaNet은 기존 detection 알고리즘에서 classifier의 loss로 사용되던 cross entropy loss에서 변형된 focal loss를 제안했습니다. Focal loss를 이용해 class imbalance 문제를 해결한 논문입니다.

### Class Imbalance

Class imbalance란 detector 학습에서 foreground와 background의 class가 극단적으로 imbalance한 경우를 뜻합니다. Foreground class란 bbox에 물체가 있는 경우를 말하며 positive라고도 불립니다. Background class란 bbox에 물체가 없는 경우를 말하며 negative라고도 불립니다. 아래와 같은 상황이 class imbalance한 상황입니다. 이러한 class imbalance한 상황이 학습 중에 발생하면 모델이 제대로 학습되지 않습니다.

> Class imbalance

![](https://user-images.githubusercontent.com/31475037/75532750-c4491580-5a57-11ea-868f-f7a3c63f213a.png)

논문에선 위와같은 상황을 막기 위해 focal loss를 제안합니다.



### Focal loss

Focal loss는 적은 수의 hard negative, 많은 수의 easy negative가 있는 상태에서 easy negative가 detector의 학습을 망치는 것을 막습니다. 기존엔 이 문제를 해결하기 위해 hard data mining과 같은 기법들이 적용되어 문제를 해결하였는데, 이 논문에서는 focal loss로 해결하였습니다. 

Focal loss는 foreground와 background가 극도로 imbalance한 경우를 다루기 위해 설계되었습니다. Easy sample에 대해서는 작은 가중치를 부여, hard sample에는 큰 가중치를 부여해서 easy negative가 학습 중의 loss를 망치지 않도록 합니다.(부스팅과 유사함)

다음과 같이 학습 중의 CE loss 결과를 봅시다. CE loss가 낮다는 의미는 모델이 객체 분류를 제 대로 했다는 의미입니다.

> CE(Cross Entropy) loss에서의 예시

<center><img src="https://user-images.githubusercontent.com/31475037/75532770-c57a4280-5a57-11ea-829c-cacfca776e9c.png" width="80%"></center>
즉, Loss 값으로 본다면 잘 분류된 샘플은 loss가 낮고, 잘 분류가 안된 샘플은 loss가 높게 나오게 됩니다.

> Loss

<center><img src="https://user-images.githubusercontent.com/31475037/75532779-c612d900-5a57-11ea-9371-b1adc1b7c544.png" width="80%"></center>
그런데 이미지에서 easy example이 압도적으로 많다면 객체에 대해 제대로 학습을 못하게 되버립니다. 거기에 easy example의 대다수가 negative class면 학습을 완전히 망쳐버리게 됩니다.

> easy sample이 loss 값의 대다수를 차지

<center><img src="https://user-images.githubusercontent.com/31475037/75532866-cc08ba00-5a57-11ea-9012-578270d6ad5b.png" width="80%"></center>
그렇기에 FL(Focal Loss)는 하이퍼파라미터 λ를 이용해 CE의 loss 값을 약간 비틀었습니다. 그 결과 easy sample이 loss에서 차지하는 비율을 크게 낮추었습니다.

> CE loss와 FL loss의 값 차이

<center><img src="https://user-images.githubusercontent.com/31475037/75535226-77b20a00-5a58-11ea-80f3-a0db05ab7823.png" width="80%"></center>
### RetinaNet

RetinaNet은 backbone 네트워크와 두개의 subnetworks로 구성됩니다. Backbone 네트워크는 입력된 전체 이미지에 대해서 convolutional feature map을 계산하는 역할을 수행합니다. 첫번째 subnet은 backbone의 결과에서 object classification을 수행하는 단계이며, 두번째 subnet은 bbox regression을 하는 역할을 수행합니다..

다만 특이한점은 기존 논문들에서 classification이나 bbox regression을 FC layer를 이용해 했다면, 여기서는 convolution만을 이용해 했다는 점입니다. 이 부분은 FCN(Fully Covolutional Network)의 영향을 크게 받았지 않나 싶습니다.

![Focal Loss RetinaNet architecture](https://github.com/uk-kim/uk-kim.github.io/blob/master/_posts/2018-12-07-Focal-loss-for-dense-object-detection/retinanet_architecture.png?raw=true)



### Performence

RetinaNet의 성능은 1-stage detector임에도 불구하고 기존에 있던 2-stage detector들을 물리치고 SOTA를 찍었습니다. 특히나 기존에 어려운 방법론들을 제치고 loss를 약간 변형한 것만으로 좋은 성능을 낸 것에 주목할만 합니다.![Object Detector Comparison](https://github.com/uk-kim/uk-kim.github.io/blob/master/_posts/2018-12-07-Focal-loss-for-dense-object-detection/object_detector_compare.png?raw=true)



<br>

**참고 강의**

[object detection review](https://arxiv.org/pdf/1905.05055.pdf)

[ICCV2017 oral presentation - RetinaNet](https://www.youtube.com/watch?v=44tlnmmt3h0)

[YOLO review](https://towardsdatascience.com/yolov1-you-only-look-once-object-detection-e1f3ffec8a89)

[PR-132 SSD](https://www.youtube.com/watch?v=ej1ISEoAK5g&t=304s)