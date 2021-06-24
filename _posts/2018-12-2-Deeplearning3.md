---
layout: post
title: CNN(Convolutional Neural Network)
tags: [ML]
use_math: true
---

# CNN의 원리

컴퓨터에서는 이미지는 R, G, B 3차원의 array(행렬)로 표현됩니다. 각 pixels는 0(black)-255(white)사이의 한 값으로 표현됩니다.

아래 고양이 사진에서 height가 400 pixels, width가 248 pixels이고 R, G, B 3 channel을 가진 3차원 행렬로 표현됩니다.

> 행렬로 표현된 고양이

<center><img src="http://cs231n.github.io/assets/classify.png" width="90%"></center>
> 컴퓨터가 이미지에 대해 인식을 잘 못하는 경우

이러한 이미지를 컴퓨터가 인식을 하기 어려운 경우는 다음의 예시들이 대표적입니다.

- Viewpoint variation 

  같은 물체 일지라도 다른 각도에서 찍을 시 인식이 어려움

  ![](https://user-images.githubusercontent.com/31475037/59824788-22701580-936d-11e9-92d9-ed4f3eefbd2d.png)

- Deformation

  물체가 변형되어 있으면 인식이 어려움
  
  ![](https://user-images.githubusercontent.com/31475037/59824759-07050a80-936d-11e9-8235-0a0c2f544789.png)

- Illumination conditions

  조명에 따라 인식이 달라짐

  ![](https://user-images.githubusercontent.com/31475037/59824758-07050a80-936d-11e9-9de5-0eb1a7fd3027.png)

- Occlusion

  다른 물체에 가려져 알고자 하는 물체를 인식하기 어려움

  ![](https://user-images.githubusercontent.com/31475037/59824760-07050a80-936d-11e9-8585-f984d0a7ab94.png)

- Background Clutter

  배경과 물체가 비슷해서 구분이 어려워 인식하기 어려움

  ![](https://user-images.githubusercontent.com/31475037/59824761-07050a80-936d-11e9-8179-8393f0568ba5.png)

- Intra-class variation

  같은 클래스 내에서도 여러 모양이나 색이 나올 수 있음
  
  <center><img src="https://user-images.githubusercontent.com/31475037/59824762-079da100-936d-11e9-95cd-f4d3cfd84112.png"></center>

<br>

## CNN

> CNN의 기원

David H. Hubel과 Torsten Wiesel은 1958년과 1959년에 시각 피질의 구조에 대한 결정적인 통찰을 제공한 고양이 실험을 했습니다. 

이들은 시각 피질 안의 많은 뉴런이 작은 local receptive field(국부 수용영역)을 가진다는 것을 보였으며, 이것은 뉴런들이 시야의 일부 범위 안에 있는 시각 자극에만 반응을 한다는 의미입니다. 

뉴런의 receptive field는 서로 겹칠 수 있으며, 이렇게 겹쳐진 receptive field들이 전체 시야를 이룹니다. 추가적으로 어떤 뉴런은 수직선의 이미지에만 반응하고 다른 뉴런은 다른 각도의 선에 반응하는 뉴런이 있을 뿐만 아니라, 어떤 뉴런은 큰 수용영역을 가져 저수준의 패턴(edge 등)이 조합되어 복잡한 패턴(texture, object...)에 반응한다는 것을 알게되었습니다. 

이러한 관찰을 통해 고 수준의 뉴런이 이웃한 저수준의 뉴런의 출력에 기반한다는 아이디어를 생각해 냈습니다.

<center><img src="https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=http%3A%2F%2Fcfile22.uf.tistory.com%2Fimage%2F99F879415BC97DDD0B0E68"></center>
> 초기의 CNN 모델


<center><img src="https://user-images.githubusercontent.com/31475037/59593100-4f81b580-912c-11e9-8c46-4d796dda46b6.png"></center>
Convolution Network의 장점은 다음과 같습니다

- Locality : 인접 신호의 correlation 관계를 비선형적으로 추출함
- Sub sampling : feature 수를 줄이고 maxpooling 방식을 사용해 가장 강한 신호만 전달함

**Convloution Filter**

Convolution Filter란 무엇일까? 이제부터 Convolution Filter를 Conv로 부르겠습니다.

Conv는 영상으로부터 feature를 추출하기 위한 필터로 다음과 같은 특징을 가집니다

- Locality : 인접 신호의 correlation 관계를 비선형적으로 추출
- shared weight : 동일한 계수의 weight filter(총 파라미터 수를 줄임)

이러한 특징에 대해 알아보기 위해 Conv의 작동원리에 대해 알아볼까요?

> 그림으로 표현된 Conv의 작동 원리

여기 height가 32, width가 32, channel(depth)이 3인 이미지가 있다고 합시다.

일반적으로 컬러 이미지는 R, G, B 3채널로 표현됩니다.

이 이미지에 height가 5 width가 5 channel이 3인 Conv를 적용을 시켜 볼까요?

(이 이미지는 32x32x3의 shape를 가진다고 불립니다.)

<center><img src="https://user-images.githubusercontent.com/31475037/59593102-501a4c00-912c-11e9-9153-f22ad1b84812.png"></center>
Conv 하나를 적용하게 되면 하나의 Conv에 있는 75개의 파라미터(5x5x3)와 이미지가 dot product 연산을 통해 하나의 값을 추출해냅니다. 여기서 사용자의 설정에 따라 bias를 추가하게 되면 76개 추가하지 않으면 75개입니다.

<center><img src="https://user-images.githubusercontent.com/31475037/59593104-501a4c00-912c-11e9-9818-f7fbdeca80fb.png"></center>
이런 일련의 과정을 통해 하나의 Conv가 하나의 feature map(activation map)을 만들어 냅니다

<center><img src=""></center>
<center><img src="https://user-images.githubusercontent.com/31475037/59593106-501a4c00-912c-11e9-9fbd-32e433954b46.png"></center>
그 다음 Conv를 적용시켜 또 다른 feature map을 만듭니다.

<center><img src="https://user-images.githubusercontent.com/31475037/59593107-50b2e280-912c-11e9-9f25-4ac95d5f5323.png"></center>
이런 Conv 필터 6개를 사용해 총 6개의 feature map을 추출해내고, 추출된 feature map들은 channel 단위로 묶어서 하나의 새로운 이미지 처럼 취급됩니다.

<center><img src="https://user-images.githubusercontent.com/31475037/59593108-50b2e280-912c-11e9-8619-798ff2464b9b.png"></center>
즉, 32x32x3의 shape를 가진 이미지가 6개의 Conv를 거쳐 28x28x6의 shape를 가진 이미지로 바뀌게 됩니다.

<center><img src="https://user-images.githubusercontent.com/31475037/59593110-50b2e280-912c-11e9-8cad-6446db0d0311.png"></center>
> Conv layer가 깊어질 수록 high level feature를 추출해 나간다.

이런 Conv 연산을 하는 layer를 여러번 거침에 따라 점점더 고차원의 feature들을 추출해 낼수 있습니다.

앞 부분에 있는 layer들은 선과 같이 저차원의 feature를 뒤 부분에 있는 layer들은 texture같은 고차원의 feature를 추출해 냅니다.

<center><img src="https://user-images.githubusercontent.com/31475037/59593112-514b7900-912c-11e9-9959-b4e503dce792.png"></center>
> Conv layer와 image size

아래는 7x7 이미지에 filter size가 3x3, stride가 1인 Conv를 적용하는 모습입니다.

여기서 stride는 Conv가 다음 영역으로 이동할 때 얼마나 움직일지 지정해주는 값입니다.

<center><img src="https://user-images.githubusercontent.com/31475037/59593114-514b7900-912c-11e9-8484-60a51f3d54de.png"></center>
아래는 7x7 이미지에 filter size가 3x3, stride가 1인 Conv를 적용하는 모습입니다.

여기서 stride는 Conv가 다음 영역으로 이동할 때 얼마나 움직일지 지정해주는 값입니다.

<center><img src="https://user-images.githubusercontent.com/31475037/59593115-51e40f80-912c-11e9-86b4-572dff751896.png"></center>
다음 공식을 통해 Conv 연산자를 통해 나오는 이미지의 Width와 Height를 알 수 있습니다.(일반적으로 넣어주는 이미지는 Height와 Width를 같게 고정시킵니다.)

<center><img src="https://user-images.githubusercontent.com/31475037/59593117-51e40f80-912c-11e9-8df1-592bcfa838d2.png"></center>
Conv와 이미지의 크기가 제대로 일치하지 않는다면 연산이 제대로 되지 않겠죠. 그래서 나온 방식이 padding을 통해 크기를 맞춰주는 방식입니다.

> 이미지 padding

이미지 padding 기법은 영상처리에서 자주 쓰였던 기법으로 이미지 최 외곽을 특정값(0이 될 수도 있고, 근처 픽셀 값이 될수 도 있습니다)으로 채워주는 기법입니다.

이를 통해 Conv가 제대로 동작하도록 돕습니다.

<center><img src="https://user-images.githubusercontent.com/31475037/59593119-51e40f80-912c-11e9-8684-fa85e6d28767.png"></center>
> 실제 3 Channel image에 CNN이 적용되는 모습

실제 Conv가 적용되는 모습은 다음과 같습니다.

Conv를 일종의 직사각형 모양의 큐브 블럭이 이미지에 적용된다고 보시면 편합니다.

<center><img src="https://user-images.githubusercontent.com/31475037/59593125-527ca600-912c-11e9-86dd-cf72042afc41.png"></center>
**1x1 Conv**

1x1 Conv란 다음과 같습니다. Conv의 Filter 크기가 1인 Conv로 입력의 필터 크기 보다 Conv의 필터 수가 작을 때 Dimensionality Reduction 효과가 있습니다.

이런 기법을 통해 네트워크의 연산량을 적게 하면서도 네트워크를 깊게 쌓을 수 있도록 도와줍니다.

<center><img src="https://user-images.githubusercontent.com/31475037/59593122-527ca600-912c-11e9-9668-d8ca2e3e586f.png"></center>
**Pooling**

pooling 기법은 영상의 width와 hight를 절반으로 줄여주며, 가장 강한 신호만 추출하거나, 혹은 신호의 평균값을 전달해주는 역할을 합니다.

<center><img src="https://user-images.githubusercontent.com/31475037/59593123-527ca600-912c-11e9-8f52-1a457a02e16a.png"></center>
> Max Pooling

다음은 Max Pooling의 예시로 2x2 filter size를 가진 max pooling 연산자가 feature를 추출하는 모습입니다. filter 사이즈 내에서 가장 높은 값만 추출해 냅니다.

<center><img src="https://user-images.githubusercontent.com/31475037/59593124-527ca600-912c-11e9-9663-8af75fdff026.png"></center>
<br>

## CNN parameter vs fc parameter

입력이 36x36x1의 shape를 가진 이미지가 있다 했을 때 CNN에서의 parameter 수와 FC(Fully Cunnected)만을 사용한 네트워크에서의 paramter 수를 비교해 봅시다.

![](https://user-images.githubusercontent.com/31475037/59670831-8c66ae80-91f7-11e9-8465-76a0100ec34d.png)

<center><img src="https://user-images.githubusercontent.com/31475037/59670832-8c66ae80-91f7-11e9-9615-9a55adcb6bfd.png"></center>
위의 그림과 같이 parameter 수가 5배 정도 차이가 납니다.

<br>

## Neuron 관점에서의 Conv Layer

Fully Cunnected Layer에서는 dot product가 이루어지는 각 노드가 각 뉴런입니다.

그렇다면 Conv Layer에서는 어떤가요?

바로 Conv Layer에서는 각 필터가 각 뉴런을 의미합니다.

> 5개의 필터를 이용한 Conv

다음과 같이 입력에 대해 Conv를 5개를 썼다면, 뉴런이 5개 있다는 것과 동일한 의미입니다. 

![](https://user-images.githubusercontent.com/31475037/59965152-45552200-9545-11e9-9547-a12d8a66aef2.png)

<br>

## Receptive Field in CNN

receptive field(수용영역)는 CNN에서 가장 중요한 요소  중 하나입니다. CNN 자체가 고양이의 뇌가 사물을 받아들이는 방식을 모방하여 만든 모델이고, 사물을 인식하는데 receptive field가 얼마나 큰지가 인식에 지대한 영향을 미치기 때문입니다.

이러한 특성때문에, 최신 CNN 구조들은 receptive field에 대해 충분히 고려하여 설계 되고 있습니다.

그러나 이러한 receptive field를 어떻게 정해야 하는가에 대한 완벽한 가이드라인이 없습니다.

**recpetive field는 입력 영상의 특정 영역으로 정의 됩니다.**

특히나 receptive field의 feature는 **center location**과 **kernel size**로 결정됩니다. 

그러나 CNN에서 receptive field 안의 모든 픽셀들이 모두 동등하게 중요하다고 여겨지진 않습니다.

receptive field 안에서는 중앙에 있는 pixel 일수록 더 중요하다고 여겨집니다. 즉, feature는 특정 영역을 보는 것 뿐만 아니라, receptive field의 중앙영역을 다른 영역보다 더 집중해서 봅니다.

아래 그림에서와 같이 kernel size가 늘어나면 늘어날 수록 CNN이 볼 수 있는 영역이 늘어 난다고 생각하시면 됩니다.

> Convolution filter가 작동하는 모습과 receptive field

<center><img src="https://mlnotebook.github.io/img/CNN/convSobel.gif"></center>
그렇다면 어떻게 해야 receptive field를 넓게 만들 수 있을까요?

일반적인 방법은 크게 세가지입니다.

1. kernel size를 크게 만든다.
2. stride 값을 크게 주거나 pooling layer를 추가한다.
3. kernel에 dilation을 사용한다.(dilation convolution 사용)

첫번째 방법은 위에 그림에서처럼 간단하게 kernel size를 키워줘서 receptive field를 늘려주는 방식입니다. 그런데 receptive filter를 늘리면 parameter 수가 비약적으로 상승해 학습에 더 어려움이 생깁니다. 그렇기에 나온 방식이 큰 kernal을 작은 kernal들로 factorize하는 방식입니다.

convolution factorization을 이용하면 parameter 수는 더 줄어들면서 망은 깊어지는 효과를 볼 수 있습니다.

> 5x5 convolution을 3x3 convolution 2개로 factorize한 그림
>
> 5x5 convolution은 3x3 convolution에 비해 25/9=2.78배 연산량이 많다.

<center><img src="https://user-images.githubusercontent.com/31475037/61538066-976d6280-aa73-11e9-9fa7-86ae0af87fe4.PNG"></center>
이러한 방식을 통해 큰 kernal을 균일한 3x3으로 표현하는 것이 VGGNet의 핵심아이디어가 되었습니다.

또한 Google은 이 방식을 적용해 inception v2에서 연산량 절감 효과를 이뤄냈습니다.

> inception v1에서 v2로의 변화

<center><img src="https://user-images.githubusercontent.com/31475037/61538614-b91b1980-aa74-11e9-8817-dbf811908e06.PNG"></center>
두번째 방식은 stride를 크게 가짐으로써 convolution filter가 보는 영역을 줄여주거나, 각 pooling layer를 통해 resolution을 절반으로 줄여 CNN이 봐야하는 영역을 줄이는 방식입니다. 이러한 방식은 연산량을 크게 줄여주어 memry saving과 processing time을 크게 줄여줍니다. 하지만 그만큼 영상에서 디테일한 부분을 놓치게 되어 classification task에서는 자주 쓰이지만, Super Resolution과 같이 영상에서 디테일한 부분을 생성해야하는 네트워크에서는 잘 쓰이지 않습니다.

세번째 방식은 dilation(팽창) convolution을 사용하는 방식입니다. 해당 방식을 이용하면 많은 메모리를 사용하고 느린 processing time을 가진다는 단점이 있습니다. 이러한 dilation convolution은 recpetive field가 중요한 sementic segmentation task에서 많이 쓰입니다. 그렇지만 이러한 dilation은 feature의 디테일을 많이 놓치기에 Super Resolution task에서는 잘 쓰이지 않습니다.(dilation convolution 혹은 atrous convolution이라고도 불립니다.)

CNN의 경우 넓은 receptive field 확보를 위해 Convolution을 여러차레 겹쳐 쌓기도 하고, Pooling layer를 이용해 영상의 크기를 줄이기도 합니다.

<br>

**읽어볼만한 글**

[CNN](https://excelsior-cjh.tistory.com/180)

[CNN pytorch 코드](https://github.com/chacha95/Deeplearning_example/blob/master/05.CNN.ipynb)

[intuitive understanding about CNN](https://medium.com/apache-mxnet/1d-3d-convolutions-explained-with-ms-excel-5f88c0f35941)

[CNN introduction](https://towardsdatascience.com/a-comprehensive-introduction-to-different-types-of-convolutions-in-deep-learning-669281e58215)



**참고 강의**

[CS231n](https://www.youtube.com/playlist?list=PL3FW7Lu3i5JvHM8ljYj-zLfQRF3EO8sYv)

<br>

