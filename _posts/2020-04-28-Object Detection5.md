---
layout: post
title: Detectron2 리뷰
tags: [Object Detection]
use_math: true
---

# Detectron2란?

[Detectron2](https://github.com/facebookresearch/detectron2)란 FAIR(Facebook Artificial Intelligence Research)에서 만든 pytorch 기반 object detection과 sementic segemanation을 위한 training/inference 플랫폼입니다. 

> Detectron2

![](https://miro.medium.com/max/4000/0*VbMjGBHMC6GnDKUp.png)



### 왜 Detectron2가 좋은가?

딥러닝 모델 연구를 흔히 아이(연구자)가 블록(레이어)를 안정적으로 쌓아 올리는 과정에 비유합니다. 그런데 아이가 밑바닥부터 블록을 쌓아 모델을 완성하는 작업은 굉장히 어렵습니다. 그렇기에 완성된 블록(detectron2)를 이용해 개발을 하면 굉장히 편리하게 개발을 할 수있습니다.

> 딥러닝 모델 개발 과정

![](https://user-images.githubusercontent.com/31475037/80436637-8f175180-893a-11ea-9224-68104f0d14e9.png)

또 Github에 올라온 코드 중에는 잘못된 구현이나, 최적화가 안되 있는 코드가 많습니다. Detectron2는 FAIR에서 체계적으로 최적화시킨 모델이며, [benchmark](https://detectron2.readthedocs.io/notes/benchmarks.html) 상에서도 다른 오픈소스보다 좋은 성능을 보였습니다. 

Faster R-CNN 계열 모델 뿐만 아니라, FAIR에서 최근 연구하는 논문 구현체를 Detectron2에 업데이트 하고 있습니다.



### Detectron2 특징

Detectron2는 학습 루프를 pytorch로 짜인 engine을 통해 학습합니다. 기존에 pytorch에서는 보지 못했던 스타일이며, caffe2 학습 스타일을 pytorch로 짠 것입니다. 

Detectron2를 이용해 학습을 하게 되면, 우리가 일반적으로 딥러닝 모델을 짤 때 구현하던 training loop를 짜지 않고 engine을 이용합니다. engine 사용은 학습 과정을 추상화 할 수 있어, 연구자 혹은 개발자는 모델 개발 자체에만 집중하게 됩니다. 즉, 모델을 구축하고 모델에 입력과 출력을 어떻게 할지만 정의하면 나머지 학습하는 부분은 detectron2에서 해줍니다.

최근들어 여러 학습 기법들(learning rate decay, learning rate warmup)등의 기법들이 발달하고 있지만, 실제 적용해 좋은 성능을 내기가 어려운데, FAIR에서는 object detection에서 자기들의 노하우를 가감없이 공개하고 있습니다.

Detectron2가 다른 오픈 소스들에 비해 빠른이유는 python 최적화가 잘되어있기도 합니다만, 그 외에 연산량이 많이 드는 부분을 python이 아닌 CUDA와 C로 구현했기에 보다 좋은 성능을 냈습니다. box iou를 계산하는 부분이나, defromable conv 부분 등은 연산량이 많이 드는 부분인데, 이 부분을 CUDA로 구현했고 이는 detectron2 내 코드에서 확인 할 수 있습니다.



### Detectron2 구성

Detectron2는 config 파일 및 model zoo 등을 폴더 별로 관리하고, 이를 학습시 파라미터로 전달해 주어 지정합니다.

다른 부분은 생략하고 가장 중요한 부분만 보자면, tools 폴더와 detectron2 폴더입니다. tools 폴더는 학습을 할 수 있는 train_net.py가 존재하며 해당 파일을 파라미터를 넣어 실행시킴으로서 학습을 합니다.

Detectron2 폴더는 모델 및 학습을 하는 engine이 정의되어 있습니다. Detectron2는 engine을 통해 학습을 하며, 사용자는 모델구조만 변경하고 하이퍼파라미터만 조절해주면 됩니다.

> detectron2의 구성

![](https://user-images.githubusercontent.com/31475037/80438017-216d2480-893e-11ea-8203-4a87f5c82b2e.png)

먼저 tools 폴더를 보면, 학습에는 2가지가 존재하며 **plain_train_net.py** 파일과 **train_net.py** 입니다.

**plain_train_net.py** 파일은 추상화가 덜 되있어 구조 파악이 쉽지만, SGD-momentum을 이용한 학습만 지원하고 나머지 기능들은 지원하지 않는다고 적혀있습니다.

**train_net.py** 파일은 training loop가 추상화가 되있어 실제 training loop가 어떻게 도는지 파악이 힘듭니다. Engine이라는 것을 사용해 학습을 하며, learning rate warmup과 같은 기능들을 사용해 학습을 진행합니다. 즉 이 engine을 이용하면 사용자는 학습과정은 신경쓰지 않고 오로지 모델과 loss만 신경쓰면 되는 구조입니다.

> tools 폴더

![](https://user-images.githubusercontent.com/31475037/80436644-90e11500-893a-11ea-9901-a0ed16303fd7.png)



Detectron2 폴더를 뜯어보면 다음과 같이 구성되어 있습니다. 그 중에서 사용자가 모델 변경 없이 사용하기 위해선 다음 3가지를 알면 됩니다.

먼저 config 폴더는 detectron2 사용시 필요한 하이퍼파라미터들이 정의되어 있고 이를 변경하여 사용하면 됩니다.

Engine은 FAIR에서 caffe 스타일의 training loop를 가져와 pytorch로 구현한 부분입니다. 이를 통해 학습 과정을 추상화시킬 수 있습니다.

[model zoo](https://github.com/facebookresearch/detectron2/blob/master/MODEL_ZOO.md)는 FAIR에서 학습한 모델들이 관리되는 폴더입니다. 

> detectron2 

![](https://user-images.githubusercontent.com/31475037/80436642-90487e80-893a-11ea-988e-50cba9e03227.png)

여기서 layers 폴더를 열어보면 C와 CUDA로 된 파일들이 나오는데, 이는 python으로 구현시 연산량을 많이 잡아먹는 부분을 빠르게 계산하기 위해 구현했습니다.

Detectron2는 API도 구현되어 있어 다음 [Colab Detectron2 API 사용법](https://colab.research.google.com/drive/16jcaJoc6bCFAQ96jDe2HwtXj7BMD_-m5)에서 처럼 쉽게 사용가능하게 만들어졌습니다.

그 외에도 학습된 pytorch 모델을 caffe2로 변환하는 코드도 제공해 엣지 디바이스에 올리기도 쉽습니다.(pytorch -> ONNX -> caffe2)

즉, Detectron2 플랫폼을 이용하면 사용자가 모델과 loss만 정의하면 학습부터 모델 포팅까지 해주면서 최적화까지 잘 되어있는 플랫폼입니다. 



