---
layout: post
title: Video Understanding
tags: [Video Understanding]
---

최근 딥러닝을 이용해 image에 대한 연구뿐만 아니라 temporal한 정보를 가진 video에 대한 연구들이 진행되고 있습니다. 대표적으로 large-scale video annotation이나 영상에 달리는 subtilte을 자동으로 만들어주거나, video에서의 사람의 행동을 이해하는 action recognition, video에서 불필요한 부분을 지워주는 video inpainting 같은 분야에서 활발히 연구되고 있습니다.

특히나 이 분야에서 가장 메인이 되는 분야는 video understanding을 기반으로 한 action classification입니다. 해당 비디오에 있는 사람이 하는 action을 인식하고, 이에 대해 분류를 하는 task입니다.

video understanding task에 필요한 training 데이터 셋과 딥러닝 이전에는 어떤기술을 사용했는지에 대해 알아볼까요?

<br>

# Video Datasets

> action classification

비디오가 들어왔을 때 어느 action class에 속하는지 분류하는 task 입니다.

- UCF 101

  Youtube videos, 13,320개의 video들과 101개의 action class를 가졌습니다.

  다양한 camera motion과 object의 외형, 자세, viewpoint, 배경, illumination을 가집니다.

  일반적으로 action classification에서 test용 benchmark로 쓰입니다.

  <center><img src="https://user-images.githubusercontent.com/31475037/60317244-1dcce200-99a9-11e9-9d28-838437179595.png"></center>

- HMDB-51

  6849개의 유튜브 video로 이루어져 있고, 51개의 action class로 나누어져 있습니다. 각 class는 최소 101개 이상의 클립으로 이뤄져 있습니다.

  일반적으로 action classification에서 test용 benchmark로 쓰입니다.

  ![](http://serre-lab.clps.brown.edu/wp-content/uploads/2012/08/HMDB_snapshot1.png)

- Sports-1M

  Youtube videos

  1,133,157개의 video들과 487개의 sports class를 가집니다.

  안타깝게도 해당 데이터셋은 요즘 잘 안 쓰이는것 같습니다.

  ![](https://user-images.githubusercontent.com/31475037/60317218-08f04e80-99a9-11e9-9ac1-dfb766f3139c.png)

- kinetic datasets

  deepmind사에서 만든 데이터셋으로 각 action class에 맞는 비디오의 유튜브 url이 들어 있습니다.

  650,000개의 비디오로 이루어져 있으며 700개의 human action class가 있습니다.

  각 클립은 10초 가량으로 이루어져 있으며 single class label이 달려있습니다.

  각 클립의 labeling은 사람이 했으며, 유튜브에 있는 유니크한 클립입니다.

  최근 action classification에서 training 데이터셋으로서 de facto(산업계 표준)로 떠오르는 데이터 셋입니다.

  [kinectis](https://deepmind.com/research/open-source/open-source-datasets/kinetics/)
  
  

> video classification

- **YouTube 8M**

  800만개의 video와 3862개의 클래스를 가졌습니다.

  해당 데이터셋을 이용한 YouTube 8M challenge가 진행되고 있습니다.

  현재로썬 video classification dataset 중에는 데이터셋 규모도 가장 크고, 매년 workshop이 진행되고 있습니다.

  [Youtube 8M](https://research.google.com/youtube8m/index.html)

  [technical paper](https://arxiv.org/pdf/1609.08675.pdf)



![](https://storage.googleapis.com/kaggle-media/competitions/youtube/YT8M.png)

![](https://user-images.githubusercontent.com/31475037/60317219-08f04e80-99a9-11e9-89f7-ca0dc424b954.png)



> Movie Querying

movie clip에 대한 설명을 다는 task 입니다.

- LSMDC(Large Scale Movie Description Challenge)

  M-VAD and MPII-MD 데이터 셋을 이용한 challenge 입니다.

  main task는 다음 3가지 입니다.

  - Movie description(영화 설명)
  - Movie retrieval(영화 검색)
  - Movie Fill-in-the-Blank(QA)

<center><img src="https://user-images.githubusercontent.com/31475037/60317846-ba907f00-99ab-11e9-9ebb-b98ed7e70641.png"></center>
> Challenges in Videos

이미지 영역에서의 task들과는 다르게 video 영역에서 task들은 다음과 같은 특징을 지닙니다.

- image dataset에 비해 video dataset의 계산량이 많다
- resolution이 낮거나, motion blur와 같은 현상이 나타난다
- 많은 training dataset을 요구한다.
- Sequence modeling을 해야 한다(RNN, LSTM과 같은 모델이 많이 쓰임)
- Temporal Reasoning

<br>

## Before Deeplearning

딥러닝이 등장하기 이전에는 Video understanding task 에서는 ME(Motion Estimation)&MC(Motion Compensation) 기법을 이용했습니다.

ME을 이용해 Motion을 예측하고 MC를 이용해 프레임 interpolation과 같은 작업을 했었습니다. 

Video Recognition task에서는 주로 ME만을 사용했기에 ME 기술에 대해 알아 봅시다.

- Motion Estimation

  Motion Estimation을 이용해 Motion Vector를 구하는 것이 목표입니다.

  Motion Estimation의 대표 기술로는 block based matching 방식인 Block-Matching-Algorithm(BMA)와 Optical Flow가 있습니다.

  - BMA

    BMA는 ME를 할 때 한 프레임 이미지를 잘게 잘라줍니다.(e.g. 8x8, 16x16)

    > block 단위로 잘린 이미지

    <center><img src="https://user-images.githubusercontent.com/31475037/60324891-f08c2e00-99c0-11e9-95a0-31f02af8c001.png"></center>
t-1번째 프레임의 블록을 기준으로 t번째 프레임에서 제일 비슷한 block을 찾아줍니다.
    

찾는 기준은 Search Range라는 윈도우를 하나 정의한 후 그 크기 안에서 가장 비슷한 블록을 찾는 방식으로 진행됩니다.
    
비슷함의 측정은 SAD(Sum of Absolute Difference)방식을 이용해 두 block이 얼마나 비슷한지 측정합니다.
    
![](https://user-images.githubusercontent.com/31475037/60324892-f08c2e00-99c0-11e9-8c8c-2ca719c7586e.png)
    
> Motion Vector

이를 통해 Motion Vector를 구해준 뒤 Motion Vector를 이용해 다음 task인 MC나 Video Recognition과 같은 task를 수행합니다.
    
<center><img src="https://user-images.githubusercontent.com/31475037/60324893-f08c2e00-99c0-11e9-8d2b-ee6d2f87d7b4.png"></center>
- Optical Flow
  
  Optical Flow는 다음과 같은 가정에 의해 진행이 됩니다.
  
  - 밝기 항상성 : 어떤 객체 상의 픽셀은 프레임이 바뀌어도 그 값이 변치 않음
    - 시간 지속성 : 영상 내의 움직임은 많지 않음
    - 공간 일관성 : 공간적으로 서로 인접한 점들은 동일한 객체에 속할 가능성이 높음
  
  이런 가정을 한 뒤, Motion Vector를 구하는데 있어 영상을 x축으로 미분하고, y축으로 미분하고, 시간축으로 미분해서 특정 식을 대입해서 Matrix를 푸는 방식입니다.
  
  optical flow를 구하는 알고리즘으로는 Lukas-Kanade방식이 있습니다.
  
  > 초음판 영상에서 Optical Flow를 이용해 Motion Vector를 구한뒤 표시
  
  <center><img src="https://t1.daumcdn.net/cfile/tistory/190218454FBC82311F"></center>

<br>

**참고 강의**

[What is video understanding?](https://medium.com/syncedreview/video-understanding-is-a-new-vista-for-ai-13be04416f56)

[Optical Flow](https://paeton.tistory.com/entry/%EC%98%B5%ED%8B%B0%EC%B9%BC-%ED%94%8C%EB%A1%9C%EC%9A%B0-Optical-Flow)

[Video description review](https://arxiv.org/pdf/1806.00186.pdf)

[Video Datasets](https://www.di.ens.fr/~miech/datasetviz/)