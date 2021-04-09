---
layout: post
title: 2-Stage detector 리뷰
tags: [Object Detection]
use_math: true
---

# 2-Stage detector 리뷰

Selective search 이후에 나온 R-CNN 기술은 object detection 기술에 있어 굉장히 좋은 성능을 냈으며, 딥러닝을 이용한 2-stage object detection 기술의 근간이 되는 논문입니다. 보통 R-CNN계열의 모델들은 region proposal을 하는 stage와 classification을 하는 stage가 명확히 구분되어 있어 2-stage detector라고 불립니다. 

> object detection에서 중요한 모델들

![](https://user-images.githubusercontent.com/31475037/75324222-d2612f80-58b9-11ea-8ae5-e1ad0e1ccfd5.png)

<br>

## R-CNN

R-CNN은 selective search를 딥러닝에 적용한 알고리즘 입니다. ILSVRC 2012에서 우승한 AlexNet 모델을 CNN 모델로 사용했으며, pretrained된 AlexNet 모델을 다시 학습하는 transfer learning을 이용해 성능을 올렸습니다.

R-CNN의 과정은 다음과 같습니다.

1. selective search를 통해 RoI(Region Of Interest)를 약 2000개 가량 추출 합니다.
2. RoI의 크기를 조절해 동일한 사이즈로 만듦니다.
3. RoI를 CNN에 넣어줘 feature를 추출합니다.
4. CNN을 통해 나온 feature를 SVM에 넣어줘 classification 해줍니다.
5. CNN을 통해 나온 feature를 regression을 통해 bounding box를 예측해 줍니다. 

> R-CNN

![](https://user-images.githubusercontent.com/31475037/74123157-088f8580-4c11-11ea-8555-39cfb5d770c0.gif)

CNN을 통해 나온 bounding box가 정확하지 않기에 bounding box의 위치를 보정해주는 간단한 regression을 진행해줍니다.

> Bounding box(bbox) regression

![](https://user-images.githubusercontent.com/31475037/74116008-f1db3580-4bf4-11ea-8c28-bbb7de1c786c.png)



R-CNN은 좋은 성능을 냈지만 몇가지 문제점이 존재했습니다.

1. 학습이 너무 느림

   Tesla K40을 사용했을 시 84시간이 소요되며, 너무 많은 gpu 메모리 차지

2. Selective search로 찾아낸 2000개의 영역을 각각 CNN을 통해 feature를 추출하기에 연산량이 너무 많음

3. Selective search 자체가 시간이 오래 걸리는 알고리즘

4. Test(inference)에 시간이 너무 많이 걸림

5. CNN, classifier, bbox regression 세 가지를 따로 학습해야 합니다

   End-to-end 학습 방식이 아님

이러한 문제점을 해결하기 위해 Fast R-CNN이 제안되었습니다.



## Fast R-CNN

학습과는 별도로 selective search에서 얻어진 각각을 CNN에 넣는 방식이 아닌 이미지를 통째로 CNN에 넣는 방식입니다. 이 방식을 이용해 R-CNN에서 CNN 연산을 2000번 하던걸 1번으로 줄였습니다.

Fast R-CNN 구조에 대해 알기 위해서는 RoI projection과 SPPNet에 대해 알아야 합니다.

**RoI projection**

RoI projection은 feature map 상에 있는 RoI를 구하기 위한 프로세스입니다. 과정은 다음과 같습니다.

- 이미지에 대해 selective search를 통해 RoI를 구해줍니다. 
- 이미지에서 구해진 RoI 좌표값은 CNN을 통과한 feature map 상에 projection 시켜줍니다. 이를 통해 feature map 상에 RoI가 구해집니다.
- 이 때, CNN을 통과한 feature map의 spatial resolution은 입력 이미지의 spatial resolution과 동일합니다. 

<center><img src="https://user-images.githubusercontent.com/31475037/74294049-1283c680-4d80-11ea-8c68-fa1b84f52bd3.png" width="70%"></center>
이러한 RoI projection이 가능한 이유는 CNN을 통과해 추출된 feature map에 이미지와 같이 물체의 중요한 정보들이 포함되어 있기 때문입니다. 



**SPP(Spatial Pyramid Pooling)**

SPPNet은 R-CNN에서 resize(warping)하는 과정을 SPP를 이용해 고정 크기로 바꿨습니다. 이 중 SPP layer는 다양한 크기의 입력으로부터 일정한 크기의 feature를 추출해 낼 수 있는 방법입니다. 이미지를 여러 개의 일정 개수 지역으로 나눈 뒤, 각 지역에 BoW(Bag-of-words)를 적용하여 local(지역적)한 정보를 유지합니다. 해당 방식을 제안한 논문이 바로 SPPNet입니다. SPPNet에서 제안한 SPP layer는 feature map 상의 특정 영역에 대해 고정된 개수의 영역으로 나눈 뒤, 각 영역에 대해 max pooling 또는 average pooling을 취함으로써 고정된 길이의 feature를 추출하게 됩니다.



> SPPNet

![](https://1.bp.blogspot.com/-4XYvgIQ6T8E/VZEPbZyYo7I/AAAAAAAABHE/D_HccWnYK6Q/s1600/s4.jpg)



Fast R-CNN에서는 이런 SPP layer의 한 single level pyramid만을 사용하여 이를 RoI layer라 명명했습니다.

> RoI pooling layer의 작동 과정

![](http://openresearch.ai/uploads/default/original/1X/eb4fecb57d3da1f8ba67e35b444091eb5b391d35.gif)

Faster R-CNN의 과정은 다음과 같습니다.

1. 전체 이미지를 CNN에 넣어줘 feature를 추출(CNN을 한번만 사용)
2. RoI projection을 통해 feature map 상에서의 RoI를 구해줌
3. feature map 상의 RoI는 RoI layer를 통과한 후 일정한 크기의 feature가 추출되서 나옴
4. 추출된 feature는 fc layer(fully cunnected layer)를 통과해 나온뒤, classification과 bounding box regression을 해줌, 이 때 각각을 따로 학습하는 것이 아닌 한번에 학습해주는 end-to-end 학습을 함 

> Fast R-CNN

![](https://user-images.githubusercontent.com/31475037/74889922-c959f500-53c5-11ea-8fc4-0d1bd3ed1a8e.gif)



R-CNN과 fast R-CNN의 차이는 다음과 같습니다.

> R-CNN과 Fast R-CNN의 비교

![](https://user-images.githubusercontent.com/31475037/74124107-86a15b80-4c14-11ea-832f-90f98776c18d.png)

> 간략히 비교해본 차이점

![](https://user-images.githubusercontent.com/31475037/74906022-1bfdd600-53f3-11ea-82cb-3a25f2b271ec.png)

하지만 Fast R-CNN에서는 여전히 다음과 같은 문제가 남아있습니다.

- Region proposal(selective search) 방식이 CPU에서 동작함
- CNN 외부에서 region proposal이 진행됨
- 2.32초의 test 과정중 region proposal에 2초가량이나 시간이 소모

> Region proposal에 너무 많은 시간이 소모됨

![](https://user-images.githubusercontent.com/31475037/74124112-886b1f00-4c14-11ea-91d9-1861d26d890c.png)

## Faster R-CNN

Faster R-CNN은 기존에 Fast R-CNN이 가진 속도 문제를 해결하기위해 제안되었습니다. 문제가 되던 selective search 부분을 RPN(Region Proposal Network)으로 대체하였습니다. 즉 selective search를 통해 region proposal을 하던것을 RPN을 통해 region proposal을 합니다.

Faster R-CNN의 학습은 크게 RPN 학습과 detector학습으로 구분됩니다. 학습시 이미지에서 feature를 추출하는 CNN은 공유가 됩니다.(논문상에선 shared CNN이라 나오며, 이후 backbone이라고 보통 불립니다) detector는 Fast R-CNN과 동일합니다.

> Faster R-CNN의 구조

![](https://i1.hdslb.com/bfs/archive/bfe6c083590272fbb8fcdbc94859c96e866de9a5.jpg)

**RPN 학습**

RPN은 내부 feature map의 영역내에서도 충분히 객체의 위치, 특징정보가 남아 있기 때문에 이 feature map 정보를 통해 학습을 하는 방식입니다. 

RPN에서 각각의 영역을 어떻게 학습할지에대해 논문에서 도입한 개념이 anchor box입니다. Anchor를 중심으로 anchor box를 설정해 feature map에서 영역을 설정합니다. 저자는 anchor box를 사용하면 기존 방법들과는 다르게 translation-invariance(이동에 불변)하다고 주장합니다. 또 anchor box를 사용하면 모델의 사이즈를 크게 줄일 수 있다고 말합니다.

> Translation-invariance

![](https://user-images.githubusercontent.com/31475037/74295754-4ca39700-4d85-11ea-96ef-99320b686da5.png)

RPN 이전엔 translation-invariance하게 만들기 위해 여러가지 시도들이 존재해왔습니다.

아래 그림의 (a)는 이미지 scale을 조절해 각 이미지 크기별 이미지 정보를 수집하는 multi scale 방식, (b)는 다양한 filter size를 이용하는 방식, (c)가 다양한 anchor box를 사용하는 방식입니다. 저자는 기존 방법 대비 성능과 속도 측면 모두 우수하다고 주장합니다.

> 기존 방법론과 anchor box 방법론의 차이

![](https://user-images.githubusercontent.com/31475037/74295756-4ca39700-4d85-11ea-9a1b-0f9a7fe64e1d.png)



RPN 학습은 객체를 분류하기 위한것이 아닌, 객체가 있는 영역인 positive anchor box를 RPN이 잘 찾는 것이 목표입니다. 논문에서 제안하는 positive anchor box와 negative anchor box의 정의는 다음과 같습니다.

- positive anchor box: GT box와 IoU가 0.7이상
- negative anchor box: GT box와 IoU가 0.3이하
- GT box와 IoU가 0.3~0.7 사이의 값은 사용하지 않음

논문에선 k개의 anchor box를 정의해 사용한다고 나와있습니다. 논문에서 실제 사용한 anchor box는 크기에 따라 3가지, 가로 세로 비(ratio)가 다른 3가지를 포함해 총 9가지입니다. Scale 종류는 [128x128, 256x256, 512x512], ratio 종류는 [2:1, 1:1, 1:2] 입니다.

> anchor

![](https://user-images.githubusercontent.com/31475037/74295757-4d3c2d80-4d85-11ea-8691-c16c64fd96e8.png)

이렇게 다양한 anchor를 기반으로 k개의 anchor box가 적용되어 multi-scale에 대해 학습하게 됩니다.

> anchor와 anchor box가 실제 적용된 예시

![](https://user-images.githubusercontent.com/31475037/74295761-4dd4c400-4d85-11ea-8a26-5387a5a063b8.png)

이렇게 만들어진 anchor 값들은 한 이미지당 랜덤하게 256개를 sampling합니다. Sample은 positive anchor : negative 비율을 1:1 비율로 섞어 RPN에 넣어주면, 해당 anchor에 object가 있는지 없는지 이진 분류를 하는 classifer를 학습하고, 앵커 내 물체 위치를 찾는 bbox regression을 해줍니다. 만약 positive anchor의 개수가 128개보다 낮을 경우, 빈 자리는 negative anchor sample로 채웁니다.

RPN의 학습에는 다음과 같은 loss 함수가 사용됩니다.

> loss 함수

![](https://curt-park.github.io/images/faster_rcnn/LossFunction.png)



- **pi**: Predicted propability of anchor
- **pi***: GT label(1: positive anchor, 0: negative anchor)
- **Lcls**: object 인지 아닌지 판별하는 binary classification 부분, cross entropy를 이용해 loss를 구해주는 부분
- **Lreg**: bbox regression을 해주는 로스 함수 
- **Ncls**: classification loss의 normalize를 위한 하이퍼파라미터, 논문에선 256으로 설정
- **Nreg**: regression loss의 normalize를 위한 하이퍼파라미터, 논문에선 2400
- **lambda**: 밸런스를 조절하는 하이퍼파라미터로 Ncls와 Nreg의 차이로 발생하는 불균형을 방지하기 위해 사용됨. Ncls가 256이고 Nreg가 2400이라 했을 때, lambda 값은 10으로 설정함
- **ti**: Predicted bbox 좌표
- **ti***: GT bbox 좌표

이 중 bounding box regression을 해주는 loss 함수에서 bbox의 4개 좌표값에 대해 smooth L1 loss를 이용해 regression을 해줍니다. Smooth L1 loss는 L1 혹은 L2 loss보다 outliers에 덜 민감해 bbox regression에서 자주 사용됩니다.

> smooth L1 loss를 이용해 bbox regression을 해줌

![](https://curt-park.github.io/images/faster_rcnn/SmoothL1.png)



> L1, L2, smooth L1의 차이

![](data:image/png;base64,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)



**Detector 학습**

RPN을 학습시킨 후 RPN과 CNN 부분을 freeze(고정)시킨 후 detector만 학습합니다. Detector는 fast R-CNN에서 쓰이던 것과 동일합니다.

Detector를 학습시키는 loss는 RPN과 거의 비슷합니다. 다만 classification시 binary classification을 하는게 아닌, multi-class classification을 해줍니다. 즉, 분류해야될 클래스가 2개가 아닌 여러개입니다.

> Detector 학습

![](https://user-images.githubusercontent.com/31475037/74295747-4ad9d380-4d85-11ea-9be8-f6e63e901f6f.png)

**전체 학습 과정**

본 논문에서 제안하는 전체 학습 과정은 다음과 같습니다.

1. RPN과 CNN만 따로 학습합니다.
2. RPN과 CNN은 freeze한채로 detector만 학습합니다.
3. CNN은 freeze한채로 RPN만 finetune합니다.
4. RPN과 CNN은 freeze한채 detector만 finetune합니다.

![](https://user-images.githubusercontent.com/31475037/74299120-0f440700-4d8f-11ea-84de-585dcf1d0198.png)



본 논문에선 4 스텝으로 나누어진 복잡한 학습 방법을 제안했지만, 저자가 나중에 둘다 한번에 학습하는 방법을 공개해서 현재는 4 스텝 학습 방식은 쓰지 않습니다.

### 후처리 기법

Faster R-CNN에선 두가지 후처리 기법을 사용했습니다. 그것은 threshold를 이용한 불필요한 bbox 제거입니다.

첫번째는 **confidence score threshold**이고, 두번째는 **NMS threshlod**입니다.

Faster R-CNN 모델이 bbox에 대해 예측을하고, 각 bbox에 대한 confidence score를 예측합니다. 예측한 값 중에서 confidence score threshold 보다 낮은 값은 bbox로 사용하지 않습니다. 이후 남은 bbox 중에서 겹치는 bbox를 제거하는 과정이 NMS(Non Maximum Supression) 과정입니다. bbox 끼리 IOU가 NMS threshold보다 높으면, 겹치는 bbox 중에서 confidence score가 가장 높은 bbox만 남기는 작업입니다.

**Non-Maximum Suppression(NMS)**

RPN이 region proposal을 해 region을 예측하면, 한 객체당 여러개의 region를 얻습니다. 이 중복 문제를 해결하기 위해서 NMS 알고리즘을 이용해 region의 개수를 줄입니다. NMS는 RPN에서 나온 region의 class값(object인지 아닌지 판별하는 확률값)으로 판별합니다. Class 값이 threshold를 넘는 region만 남깁니다. 논문에서 사용된 threshold 값은 0.7입니다. 또 NMS 이후, class 값이 N번째로 큰 bbox까지만 사용합니다. 논문에서 test시에는 N값이 300, training시에는 N값이 2000입니다.

> NMS

![](http://incredible.ai/assets/images/faster-rcnn-nms-algo.jpg)



### Implemantation details

논문상에서 친절히 설명되어 있지는 않지만, 실제 구현된 코드를 통해 파악한 부분은 다음과 같습니다.

- NMS

  RPN 학습시 검출된 bbox 중 NMS를 통해 2000개만 남긴 뒤, 그 2000개의 bbox 중에서 256개의 bbox만 random sampling을 합니다.

- bbox 크기

  논문상에서 사용된 bbox의 크기가 [128, 256, 512]라고 적혀있는데 이는 입력 이미지 크기에서의 bbox 크기입니다. 따라서 실제 feature map에 적용되는 bbox 크기는 입력 이미지의 크기가 feature map 크기만큼 줄어드는 subsampling size만큼 줍니다. Feature map 상에서 사용된 bbox의 크기는  [8, 16, 32]입니다.

- anchor의 수

  anchor는 feature map 상 위의 모든 픽셀에 다 찍습니다.

- 학습시 boundary 문제

  anchor box를 칠 때, 많은 수의 anchor box들이 이미지 boundary에 있는 영역을 포함합니다. 논문에선 그러한 anchor box 들을 train시에는 사용하지 않고, test시에만 사용한다고 말합니다. 예를 들어 1000x600 이미지에서는 약 20000(60 x 40 x 9)개의 anchor가 생기는데 이중 약 6000개의 anchor만 학습에 사용한다고 합니다.

Faster R-CNN의 예시는 다음과 같습니다. 입력 이미지가 800x800이라고 했을 때, CNN을 통과해 나온 feature map의 크기가 50x50이면 subsampling이 16이 됩니다. 

> feature extraction

![](https://miro.medium.com/max/1055/1*-kY2d--m-MauFNEsRw-2zg.png)

그렇게 생성된 50x50 feature map의 각 픽셀마다 anchor를 박습니다. 생성된 anchor는 총 50x50 = 2500개 입니다.

800x800 이미지 위에서 표현된 anchor는 다음과 같습니다.

> image 위에서의 anchor 표현

![](https://miro.medium.com/max/646/1*f-AxsYA9ys5wtiY9NDZh9Q.png)



Bbox 크기가 [128, 256, 512]이라 하면, 이미지에서 적용되는 bbox는 다음과 같이 표현 가능합니다.

> anchor box의 생성

![](https://miro.medium.com/max/647/1*cPidpSRVUVgv3YeY9Fc11Q.png)



그렇게 생성된 bbox 크기는 실제 feature map 에서는 subsampling 되어서 다음 크기로 feature map 상에서 object를 찾게 됩니다.

> 생성된 bbox의 크기

![](https://user-images.githubusercontent.com/31475037/74813109-155e5880-5338-11ea-8295-666afe1f9285.png)

Anchor를 모든 픽셀마다 박기에 한가지 물체에 여러 bbox가 생기는 일이 많이 일어나기에 NMS같은 알고리즘을 사용해 연산량을 줄여줍니다.



**Performence**

Faster R-CNN은 기존 R-CNN 계열의 기술들 보다 속도적인 측면, 성능적인 측면 둘다 좋은 결과를 냈습니다. 특히나 속도적인 측면에 있어 괄목할만한 결과를 냈습니다.

> Faster R-CNN의 성능

![](https://bloglunit.files.wordpress.com/2017/06/20170525-research-seminar-google-slides-2017-06-01-20-27-50.png?w=508&h=177)



<br>

## FPN

전통적인 컴퓨터 비전 알고리즘에서 많이 쓰이던 알고리즘 중 하나는 이미지를 여러 scale로 resize 한 뒤 그 각각으로부터 feature를 뽑아내는 방식이 존재합니다. 이러한 방법을 **feature pyramid**라 부릅니다. 대표적으로 SIFT나 HOG가 있습니다. 

하지만 딥러닝이 등장한 뒤 object detection 분야에서 feature pyramid 방식은 computation cost가 높고 memory 문제가 존재했습니다. 왜냐하면 각각의 scale에 대해서 CNN을 통해 feature를 뽑아야 했기 때문입니다.

> 전통적인 feature pyramid 기법

<center><img src="https://user-images.githubusercontent.com/31475037/74503142-52d77600-4f33-11ea-808d-50106668ffa7.png" width="70%"></center>
CNN을 통해 feature를 뽑는 방식은 single feature map이라고 불립니다. 이미지가 있을 때 CNN을 통해 순차적으로 feature map을 뽑고 마지막 feature map에서 prediction을 하는 구조입니다. CNN은 Bottom-up 방식으로 이미지를 본다고도 불립니다.


> CNN을 통한 feature 추출

<center><img src="https://user-images.githubusercontent.com/31475037/74511677-def49800-4f49-11ea-96ff-9ed82d42dc8e.png" width="70%"></center>
다음으론 SSD(Single Shot Detector)에서 제안한 방식입니다. 마지막 feature map에서만 prediction을 하는 것이 아니라, 각 feature map 마다 prediction을 하는 방식으로, 중간 중간 나오는 feature map을 활용하는 방식입니다.

> hierarchy한 방식

<center><img src= "https://user-images.githubusercontent.com/31475037/74511676-de5c0180-4f49-11ea-89e1-9e1680ac08bd.png" width="70%"></center>
마지막으론 FPN 저자가 제안하는 방식이 있습니다. FPN의 주요 아이디어는 Bottom-up 방식의 feature 추출과 Top-down 방식의 feature 추출 두가지를 모두 사용합니다. 

> FPN에서 제안하는 방식

<center><img src= "https://user-images.githubusercontent.com/31475037/74511675-de5c0180-4f49-11ea-96fa-730c64517ead.png" width="70%"></center>
### Top-down 과 Bottom-up

Top-down과 Bottom-up 둘다 이미지의 feature를 뽑는지에 대한 접근 방법입니다. Bottom-up은 이미지의 픽셀 단위부터 특징을 계산해서 전체적인 형태나 특징을 찾아내는 방식입니다.

> 대표적인 Bottom-up 방식인 CNN

![](https://user-images.githubusercontent.com/31475037/74509490-f9784280-4f44-11ea-8789-036d2a8f2095.png)

Top-down의 접근방식은 이미지 전체를 보고 해당 이미지에서 원하는 특징을 찾아 내는 방식입니다. 사람의 눈이 사물을 인식하는 방식이 보통 Top-down 입니다.



### FPN 구조

FPN의 저자는 이러한 Top-down 방식과 Bottom-up 방식을 둘다 사용하면서 두가지 방식에서 추출된 feature map을 lateral cunnection(측면 연결)을 통해 각각의 stage마다 prediction을 하는 설계를 하였습니다. 이러한 설계를 통해 중간 중간 feature map들이 잘못된 feature를 추출하지 않으면서도, Top-down, Bottom-up 방식 둘다 사용하는 결과를 내었습니다. 

논문에서는 Bottom-up은 기존의 CNN을 사용하였으며, Top-down은 upsampling 기법을 사용하여 높은 해상도의 이미지를 만들도록 하였습니다. 여기서 lateral-cunnection을 사용해 bottom-up에서 추출된 feature map을 재사용하였습니다.

같은 classifier/regressor를 사용하기에 채널이 전부 같고, 논문에서는 256채널을 사용했습니다. 때문에 lateral-cunnection을 이용해 feature를 합칠 때 1x1 Conv를 이용해 채널을 맞춰줍니다.



> FPN 구조

![](https://user-images.githubusercontent.com/31475037/74509754-9e931b00-4f45-11ea-858f-b77b08141d3d.png)



### FPN + Faster R-CNN

FPN은 기본적으로 RPN의 컨셉을 많이 가져온 네트워크이나 몇가지 차이점이 존재합니다. 먼저 RPN에서 사용됬던 anchor를 여기서도 사용했습니다. 다만 Pyramid 각각의 층별로 scale이 달라 다양한 scale의 anchor는 사용하지 않고 다양한 ratio의 anchor를 사용했습니다. RPN에서 쓰인 ratio는 [1:2, 1:1, 2:1] 입니다.

저자는 Faster R-CNN 구조에서 RPN을 FPN으로 바꾼 구조를 제안했고, mAP 측면에서 더 나은 결과를 보여줬습니다. 

> 결과 분석

![](https://user-images.githubusercontent.com/31475037/74510234-b4551000-4f46-11ea-83bc-77f7b95c5dcd.png)



<br>

**참고 강의**

CS231N

[hoya012 blog](https://hoya012.github.io/blog/Tutorials-of-Object-Detection-Using-Deep-Learning-how-to-measure-performance-of-object-detection/)

[Google 머신러닝](https://developers.google.com/machine-learning/crash-course/classification/precision-and-recall?hl=ko)

[Precision and Recall에 대한 해석](https://darkpgmr.tistory.com/162)

[Lunit Blog](https://blog.lunit.io/2017/06/01/r-cnns-tutorial/)

[Guide of Faster R-CNN based on Pytorch Code](https://medium.com/@fractaldle/guide-to-build-faster-rcnn-in-pytorch-95b10c273439)

