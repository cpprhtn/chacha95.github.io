---
layout: page
title: Projects
subtitle: awesome projects!
---

## 딥러닝을 활용한 교차편집 영상 생성

### 교차편집이란?

교차편집(Stage Mix)란 아이돌 그룹의 여러 무대를 사용자가 임의로 편집하여 하나의 영상으로 만든 것 입니다.

> 교차편집 영상

<center><img src="https://media.giphy.com/media/LS8UsIS6nxtihtCteS/giphy.gif"></center>
해당 교차편집 영상은 현재 유튜브에서 많은 조회수가 나오고 있고, 2차 창작물로 인기를 끌고 있습니다.

하나의 영상을 만드는데 숙련된 사람들도 5시간 이상, 많을 경우 10시간 이상의 시간이 걸리기도 합니다. 이러한 영상의 제작은 제작하는 사람의 숙련도에 따라서 퀄리티의 차이가 많이 나기에, 편집자의 능력이 중요합니다.

> 왼쪽은 자연스럽지 않은 영상, 오른쪽은 자연스러운 영상

<center><figure class="second">
	<img src="https://media.giphy.com/media/hS9XQCdHYCRsgH2G2j/giphy.gif" width="49%">
	<img src="https://media.giphy.com/media/f5YN8gaHg0OcKW8n9W/giphy.gif" width="49%">
</figure></center>



### 데이터셋 구축

교차편집 영상의 퀄리티에 영향을 미치는 것은 여러가지가 있겠지만, 그 중 데이터셋과 가장 연관성이 높은 부분은 바로 안무의 난이도 입니다. 일반적으로 안무의 난이도가 쉬울수록 교차편집이 쉬워지며, 자연스러워 집니다.

> 데이터 구분

![](https://user-images.githubusercontent.com/31475037/65845967-171e8400-e377-11e9-8ff8-497e7842c2c6.PNG)



특히나 안무가 쉬운 여자아이돌의 경우 카메라 워킹이 아이돌의 얼굴 위주로 화면을 잡기에, 자연스러운 교차편집 영상 제작이 비교적 쉽습니다. 반면 안무가 화려하고 전체적인 칼같은 군무를 가진 보이그룹들은 자연스러운 교차편집이 어렵습니다.

> 왼쪽은 안무 난이도가 낮은 영상, 오른쪽은 안무 난이도가 높은 영상

<center><figure class="second">
	<img src="https://media.giphy.com/media/M9g9nywSeOJC6sQWA0/giphy.gif" width="49%">
	<img src="https://media.giphy.com/media/QutIMVU5ZAp23HDSuM/giphy.gif" width="49%">
</figure></center>



나눠진 데이터셋에 대해 OpenCV를 이용해 viewer를 만들고 labeling을 해줍니다. 원본 영상으로 8개의 영상이 주변부에 띄워지고, 중앙에는 교차편집 영상이 띄워집니다. 그에 맞춰 사용자가 키보드를 이용해 각 프레임 별로 몇 번 비디오가 정답 비디오인지 입력합니다.


> labeling 예시

<center><img src="https://media.giphy.com/media/jQDxXNTddpYbBQZaTh/giphy.gif"></center>

### 모델

제안하는 모델은 다음과 같습니다. 먼저 8개의 원본 영상을 입력으로 받습니다. 입력받은 각각의 영상은 pretrained 모델을 통과해 feature를 추출한 후, concat한 뒤 학습할 ResNext-101 모델에 넣어줍니다.

이후 ResNext-101은 각 프레임 별로 정답 비디오 클래스를 맞춘뒤 Ground Truth와 Cross-Entropy Loss로 측정해 학습을 진행합니다.

> 제안하는 모델

![](https://user-images.githubusercontent.com/31475037/65849969-ac297900-e387-11e9-9973-50f136c061a2.PNG)



Pretrained 모델은 [이 논문](https://arxiv.org/pdf/1711.09577.pdf)을 참조해 선정하였고, Video Action Classification 분야에서 쓰이는 모델을 가져와 feature 추출기로 사용했습니다. 논문과 관련되서는 해당 [Post](https://chacha95.github.io/2019-07-10-VideoUnderstanding4/)에서 자세히 설명되어 있습니다.

> pretrained 모델(ResNext101)

![](https://user-images.githubusercontent.com/31475037/65849967-acc20f80-e387-11e9-8b42-cc86c78085ad.PNG)



이후 추출된 feature를 ResNext101에 넣어주어 학습을 해주어 각 프레임 별 비디오 클래스를 예측합니다.

> 학습해야 할 모델(ResNext101)

![](https://user-images.githubusercontent.com/31475037/65849965-acc20f80-e387-11e9-9147-327e21c21309.PNG)



### 개발환경

- Pytorch 1.1
- OpenCV 3.2
- Python 3.6
- Window/Linux



<br>



## Clo3d를 이용한 3D human 데이터셋 생성

최근 3d human reconstruction 분야에 딥러닝을 이용한 연구가 한참입니다. 하지만 그렇게 생성된 3d human 모델링들은 옷의 영향력이 제거되지 않은 상태로 3d human 모델링이 생성되는 태생적인 문제가 있습니다. 

이러한 옷의 영향력이 제거되어 알몸 상태를 정확하게 만들기 위해 합성된 데이터셋을 만드는 것이 이 프로젝트의 목표입니다.

### Rendering

먼저 [SURREAL 데이터셋](https://www.di.ens.fr/willow/research/surreal/data/) 으로 부터 SMPL shape와 pose 파라미터를 가져옵니다. 가져온 shape와 pose를 기반으로 3d mesh(obj 파일)를 생성한 뒤, clo3d로 로드합니다. 로드된 mesh에 옷을 로드해 렌더링 해줍니다. 이후 animation 파일을 로드해와 clo3d를 이용해 200 frame의 animation을 만듭니다.

> Clo 3d를 이용한 rendering

![](https://user-images.githubusercontent.com/31475037/65850165-ac764400-e388-11e9-96cb-5cdc165c497f.PNG)

이러한 작업이 스크립트를 통해 자동으로 이뤄지기 때문에 옷이 제대로 입혀지지 않은 경우가 생기기에 이러한 오류를 잡아주기 위해 수작업이 많이 필요합니다.

> Rendering 에러가 생길시 수작업을 통해 수정해줘야 함

![](https://user-images.githubusercontent.com/31475037/65850160-abddad80-e388-11e9-907b-23cbd502b26d.PNG)





### Synthesize

SURREAL dataset의 texture를 불러옵니다.

> texture

![](https://user-images.githubusercontent.com/31475037/65850168-ad0eda80-e388-11e9-8a51-57fa8174bce2.PNG)

Clo3d로 생성된 3d human animation과 적절한 배경을 골라 합성해줍니다.

> Result

![](https://user-images.githubusercontent.com/31475037/65850169-ad0eda80-e388-11e9-821d-c523385374a3.PNG)



### 옷의 영향력 제거

옷의 영향력을 나타내기 위해서, shillouette을 이용해 표현하였습니다. 옷을 입었을 때의 shillouette과 옷을 벗었을 때의 shillouette의 차이를 이용해 옷의 영향력을 나타냅니다.

> Shillouette

![](https://user-images.githubusercontent.com/31475037/65850161-abddad80-e388-11e9-920c-5872cb33d045.PNG)

> Shillouette의 차이

![](https://user-images.githubusercontent.com/31475037/65850162-ac764400-e388-11e9-87d7-fae6291e475a.PNG)



### 개발환경

- Clo3d
- Window

<br>

## 딥러닝을 이용한 불교미술 채색

#### 불교미술 채색

기존에 사람이 수작업으로 채색하던 불교미술 그림에 대해, 딥러닝을 이용해 채색을 하는 프로젝트입니다.

핵심 채색 기능은 CycleGAN을 이용해 채색을 하였습니다.

> 변상도에 대한 채색

![](https://user-images.githubusercontent.com/31475037/58854545-a2914c80-86d8-11e9-9d33-c3ce496a6abf.PNG)

#### 전처리

여러 실험 결과 딥러닝 네트워크에 들어갈 이미지를 이진화 및 노이즈 제거 연산을 하면 더 나은 결과를 보여줬습니다.

> OpenCV 함수를 이용해 이진화 시켜줌

![](https://user-images.githubusercontent.com/31475037/58854670-f69c3100-86d8-11e9-9b36-4d64f5071d9e.PNG)

#### 딥러닝 채색

사이클 겐 모델을 사용하였으며,  불교 학술 문화원으로 받은 데이터를 input으로, Discriminator에 훈련시킬 이미지 데이터셋(실제 탱화)은 구글에 쿼리를 날려 수집하였습니다.

> Unpaired 데이터셋 구성

![](https://user-images.githubusercontent.com/31475037/58854783-4549cb00-86d9-11e9-8e2c-f866b4df6c7c.png)

이러한 데이터셋을 기반으로 CycleGAN 모델을 훈련시켰습니다.

CycleGAN과 관련해선 [Post](https://chacha95.github.io/2019-08-30-Image-Translation/)에서 잘 설명해 놓았습니다.

> 전체 네트워크 구조

![](https://user-images.githubusercontent.com/31475037/58854667-f56b0400-86d8-11e9-9ed1-5ec3fc49445f.PNG)

CycleGAN을 모델로 정한 이유는, Unparied 데이터셋 구축으로 동작하고, 입력 이미지의 형태를 거의 바꾸지 않기 때문입니다. 아래 논문에 실린 결과물에서, 사과에서 오렌지로 바뀌는 경우를 보면, 사과의 형태는 유지되고 색만 바뀐것을 볼 수 있습니다.



> CycleGAN은 모양을 바꾸기 힘듬, 아래 사과에서 오렌지로 바뀐 실험 결과

![](https://user-images.githubusercontent.com/31475037/64166537-4201df00-ce82-11e9-8b9f-796fbf97103b.PNG)



#### 후보정

후보정은 PyQt 및 OpenCV를 이용해 FMM 알고리즘 및 페인팅 툴을 이용해 보정을 하였습니다.

> PyQT를 이용한 반자동 보정 시스템

![](https://user-images.githubusercontent.com/31475037/58854785-467af800-86d9-11e9-89ff-8625988b6c49.png)

<br>

### 최종 결과물

![](https://user-images.githubusercontent.com/31475037/58854671-f734c780-86d8-11e9-92f2-5738c133b815.PNG)

![](https://user-images.githubusercontent.com/31475037/58854672-f7cd5e00-86d8-11e9-954b-507ff4a61a96.PNG)



### 개발환경

- PyQT 5
- Pytorch 0.3
- OpenCV 3.2
- Python 3.6
- Window

<br>

#### 프로젝트 페이지

[Slide](https://drive.google.com/uc?id=1mWrj_n7y_87R9GHe7puwFdtTGlbau4bt) | [Code](<https://github.com/chacha95/Colorization_for_BuddistArt>)

<br>

## SVM과 LBP를 이용한 특징점 검출

LBP로 이미지의 feature를 뽑고 이를 SVM을 통해 특징점인지 아닌지 학습시켜 특징점을 검출해내는 프로젝트입니다. 기존 SIFT에 비해서 70%정도의 성능밖에 나오지 않지만 SVM의 전체적인 work flow에 대해 알 수 있는 프로젝트입니다.

#### DB 생성

SVM을 학습시키기 위한 DB를 만들기 위해 BSD500 이미지를 사용하였습니다. C++ 코드로 폴더 내 모든 이미지 파일을 읽어오고, 읽어온 이미지에서 SIFT를 이용해 Feature Point를 추출해 Vector에 좌표를 저장합니다. 저장된 좌표의 LBP 벡터를 계산합니다. LBP 벡터 추출에는 15 x 15 블록을 사용하였으며, 중간 값과 비교해 작으면 0, 크면 1을 부여하는 224차원의 벡터를 만들어 냅니다.SIFT Featrue Point의 벡터는 클래스 1(특징점)으로 분류시켜 train_Data.txt라는 파일에 저장됩니다. 저장 방식은“1 1:LBP[0] 2:LBP[1] 3:LBP[2] .... 224:LBP[223]”입니다. 여기서 SVM 이진 분류기는 1클래스와 -1 클래스 두 개로 나뉘어 지는데, 1클래스는 Feature Point 클래스이고, -1은 Feature Point가 아닌 클래스입니다. 이때 학습을 위해서는 -1 클래스 또한 만들어야 하는데, Feature Point가 아닌 좌표를 임의로 생성하며, 그 개수는 Feature Point의 개수와 같아야 합니다.(같지 않으면 분류가 제대로 되지 않음을 확인했음)

#### SVM Train(svm_learn.exe)

SVM은 SVMlight를 사용하였으며, exe 파일로 되어있어, Data_set만 잘 만들어 주면 알아서 학습을 시켜줍니다.
SVMlight가 저장된 폴더에 들어가 cmd 상에서 svmlight.exe train_Data.txt명령어를 쳐주면, 자동으로 학습이 시작된다. 이후 SVM_model이라는 파일이 학습되어 나옵니다.

![](https://user-images.githubusercontent.com/31475037/58856936-e8e9aa00-86de-11e9-85b6-4275b9fefdff.PNG)

#### Test(test_extractor.cpp, svm_classify.exe, test_rendering.cpp)

Test는 BSD test 데이터셋 중 한 장을 골랐으며, test 이미지에서 모든 픽셀의 LBP 벡터를 구합니다. 구해진 LBP 벡터는 test.txt 파일로 저장됩니다.
저장된 LBP 벡터는 학습된 SVM을 통과해 해당 값이 Feature Point인지 아닌지 판별하는 정확도 값을 추출해 냅니다. 추출된 정확도 값을 읽어 들여, test 이미지 상에서 렌더링 해줍니다. 여기서 정확도 값이 1일시 클래스 1, Feature Point라는 소리임으로 test 이미지에 렌더링 해줍니다.

> workflow

![](https://user-images.githubusercontent.com/31475037/58856937-e8e9aa00-86de-11e9-9917-a0154f6138a9.PNG)

> origin

![](https://user-images.githubusercontent.com/31475037/58856938-e9824080-86de-11e9-92e5-df9ba9634101.PNG)

> SIFT

![](https://user-images.githubusercontent.com/31475037/58856939-e9824080-86de-11e9-9be8-d73298e63b26.PNG)

> LBP_SVM

![](https://drive.google.com/uc?id=1nSL6vnWs1xpkHf2RbTSV5bhdUQ_YHkjg)

<br>

> origin

![](https://user-images.githubusercontent.com/31475037/58856941-e9824080-86de-11e9-8101-6022bded0308.PNG)

> SIFT

![](https://drive.google.com/uc?id=1MUQt0jfIV7n0IuAx_-cl7cn0n9AjsNHP)

> LBP_SVM

![](https://user-images.githubusercontent.com/31475037/58856944-ea1ad700-86de-11e9-8ea7-bf452b7e026d.PNG)

#### 개발환경

- C++
- OpenCV
- SVMLight
- Window

<br>

#### 프로젝트 페이지

 [Code](<https://github.com/chacha95/OpenCV_Project/tree/master/SVM>)
