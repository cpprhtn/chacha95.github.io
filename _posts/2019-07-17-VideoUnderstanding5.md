---
layout: post
title: Non-local Neural Networks 리뷰
tags: [Video Understanding]
---

이번에 소개시켜 드릴 논문은 FAIR(Facebook AI Research)에서 낸 Non local Neural Network 입니다. 해당논문은 CVPR 2018년에 발표 되었으며, 유명한 저자들의 참여와 함께, 논문에서 제안된 non-local block의 무궁무진한 활용성과 범용성 덕분에 엄청난 인기를 끌게 되었습니다.

non-local block을 사용하면 기존 CNN의 locality를 줄이면서도 receptive field를 적은 계산량으로 늘릴 수 있다는 장점이 있습니다. 또 long range dependency 문제에 아주 효과적인 해결책으로 작용했으며, 대표적인 long range dependency 문제가 크게 나타났던 video action recognition 문제에서 좋은 결과를 보였습니다.

특히나 재밌는 점은 기존의 computer vision에서 노이즈 제거에 쓰이던 방법론인 non local filter에서 영감을 받아 딥러닝 버전의 방법론을 제안하였는데, 요즘 들어 이러한 전통적인 image processing이나 computer vision에서 쓰이던 방법을 딥러닝 버전으로 치환시킨 방법들이 좋은 결과물을 내는것 같습니다. (Super Resolution에서 DBPN이 이런 접근으로 좋은 결과물을 냈습니다.) 

[CNN](https://chacha95.github.io/2018-12-02-Deeplearning3/) 글에서 receptive field 부분을 읽으시면 본 포스트 이해에 도움이 많이 됩니다.

<br>

## Denoising

Image Processing이나 Computer Vision에서 denoising이라하면 이미지에 있는 salt pepper 노이즈를 없애주거나 aliasing과 같은 노이즈를 제거하는 것입니다. 

> Denoising 결과물

![](https://user-images.githubusercontent.com/31475037/61578424-a286c800-ab31-11e9-8681-0e7f8a4d0167.PNG)

대표적인 denoising이라 함은 gaussian function을 이용해 블러를 시키는 방식이 있습니다.

간단히 설명 드리면, denoising을 시키고 싶은 픽셀 p에 대해서 kernel 내 픽셀들에 대해 픽셀 p와 픽셀 q의 euclidean distance(두 점 사이의 거리)값을 Gaussian function에 넣어준 값에 픽셀 q의 intesity값을 곱해줍니다. 그런다음 kernal 내 모든 픽셀에 대해 이 연산을 해주면 kernel 내 모든 픽셀들을고려해 denoising을 하는 것입니다. (픽셀들의 평균을 낸다고 생각하시면 간단합니다!)

> Gaussian Filter를 이용한 denoising

![](https://user-images.githubusercontent.com/31475037/61578616-d7e0e500-ab34-11e9-9bd5-c1b4804a920d.PNG)

​                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                  

## Non-local Means Filter(NLM Filter)

NLM Filter는 노이즈를 제거하기 위해 한 장의 이미지 내에서 유사한 영역을 찾아 평균을 취해주는 방식을 이용해 denoising을 해줍니다.

아래 이미지에서 p 지점의 경우 p의 위 아래에 q1, q2와 같이 p와 유사한 구조의 지점들이 존재합니다. 때문에 한 영상 안에서도 이러한 유사한 영역들을 모아준 후 평균을 취해주면 여러장의 영상을 평균해 준 결과와 유사한 결과를 얻을 수 있습니다. 

NLM이라는 이름에서 드러나듯이 영상의 주변부 (local) 만을 이용한 것이 아니라 영상 전체 영역 (non-local)을 활용한 필터라는 점입니다.

<center><img src="https://bloglunit.files.wordpress.com/2018/01/e18489e185b3e1848fe185b3e18485e185b5e186abe18489e185a3e186ba-2018-11-15-e1848be185a9e1848ce185a5e186ab-11-07-251.png"></center>
> NLM Filter 수식
>
> W는 normlization factor

![](https://user-images.githubusercontent.com/31475037/61578931-0660bf00-ab39-11e9-9d5e-33a2ae364b45.PNG)

## Non-local Neural Networks

우리가 일반적으로 사용하는 CNN의 경우 local 한 특성 만을 볼 수 있는 구조입니다. receptive field 크기의 관점에서 보면 local operator를 여러차례 쌓으면 receptive field의 크기를 키울 수는 있지만 아무리 많이 쌓더라도 한 번에 전체 영역을 살펴보는 non-local operator와 같을 수는 없습니다. 그렇기에 저자의 주장으로, 이런 non-local operator가 long range dependency를 해결해 준다고 말합니다.

> non local operation

![](https://user-images.githubusercontent.com/31475037/61589666-3adf8400-abe8-11e9-87cc-5b5c0f8840f4.PNG)



논문에서는 similarity를 계산하는 함수 f에 대해 여러 방법을 제안하고 이를 실험했습니다.

실험 결과, similarity의 계산 방식에 따른 결과 차이는 크게 없다고 말하며, similarity를 측정한다는 것이 큰 영향을 미친다고 말합니다.

> Similarity 계산 방식

![](https://user-images.githubusercontent.com/31475037/61589667-3adf8400-abe8-11e9-9433-6c8def3e2ac5.PNG)



> 함수 f로 similarity를 계산하고 g 함수로 weight와 인풋 픽셀값을 곱해 최종 결과를 내줍니다.

![](https://user-images.githubusercontent.com/31475037/61589668-3adf8400-abe8-11e9-8aed-2921bcdca425.PNG)

> 그림으로 표현된 non-local operation

![](https://user-images.githubusercontent.com/31475037/61589664-3a46ed80-abe8-11e9-9c38-3c38a75c1102.PNG)



**Non-local opertation과 self-attention**

저자는 NLP의 Transfomer에서 제안한 self-attention 구조를 non-local operation이라고 볼수 있다고 주장합니다.

> Self-attention

<center><img src="https://user-images.githubusercontent.com/31475037/61589712-2780e880-abe9-11e9-8485-094a1785a086.PNG"></center>
### Experiment

실험을 위해서 ResNet-50 C2D 모델과 I3D 모델을 사용했습니다.

C2D 모델의 경우 기존의 ImageNet으로 pre-trained 되어있는 ResNet-50을 그대로 가져온 모델입니다. 기존의 ResNet-50의 경우 2D 이미지를 대상으로한 모델인데, 여기서는 Time 축이 추가되어 3D 형태의 영상을 봐야 하기 때문에, 중간에 pooling하는 부분만 Time축으로도 pooling을 수행해 줍니다.

> C2D

<center><img src="https://bloglunit.files.wordpress.com/2018/01/ec8aa4ed81aceba6b0ec83b7-2018-01-12-ec98a4ed9b84-5-52-49.png?w=447&h=379"></center>
다음으로 사용한 모델은 Inflated 3D ConvNet (I3D)라고 명명한 모델입니다. 이 모델은 또 두 가지 버전으로 나뉘는데 두 버전 모두  C2D와 형태는 거의 동일하고 중간에 res-block 내부에서 최초의 1×1 conv 대신에 3x1x1 conv를 사용하는 버전 하나와 중간의 3×3 conv대신에 3x3x3 conv를 사용하는 버전 하나가 존재합니다. 이 모델역시 ImageNet으로 pre-trained되어있는 모델을 가져와서 사용하는데, Time축으로 커널의 크기가 확장된 conv의 경우에는 그대로 파라미터를 가져오면 파라미터양이 다르므로, 시간으로 파라미터를 그래도 복사해서 집어 넣되, 양이 3배 늘어난 만큼 파라미터의 값은 1/3하여 집어 넣었습니다. 

I3D에 대한 자세한 설명은 [이 포스트](https://chacha95.github.io/2019-07-04-VideoUnderstanding3/)에서 설명되어 있습니다.

저자는 많은 실험을 통해 non-local block의 효율성과 성능을 보여줬는데요, 그 중 흥미로웠던 점은 similarity 계산 방식에 따른 결과 차이 였습니다.

아래 결과 표에서와 같이 계산 방식에 따른 성능 차이는 거의 없습니다.

> Similarity 계산 방식에 따른 결과 차이

<center><img src="https://user-images.githubusercontent.com/31475037/61590949-d1b53c00-abfa-11e9-99ff-9e0e10db8570.PNG"></center>
또한 non-local block을 어디에 집어넣었는냐에 따른 결과물 차이입니다.

> top-5 기준에선 res5 직전에 넣어주는게 제일 좋은 성능을 보여줌

<center><img src="https://user-images.githubusercontent.com/31475037/61590950-d1b53c00-abfa-11e9-9960-7da191c48b76.PNG"></center>
저자는 C2D 네트워크에 non-local block을 집어넣어 성능 및 효율성을 보여줬습니다. 특히나 별다른 구조 변경 없이 non-local block을 넣어주기만 해도 top-1 error에서 2%의 성능 향상을 가져다 준 점이 대단한거 같습니다.

> non-local block의 효율성과 성능을 보여줌

<center><img src="https://user-images.githubusercontent.com/31475037/61590947-d1b53c00-abfa-11e9-8468-917ec944f09b.PNG"></center>
다음으론 I3D 모델에 non-local block을 넣어준 실험결과이고요, 역시나 좋은 결과를 냈습니다.

> I3D + non-local block

<center><img src="https://bloglunit.files.wordpress.com/2018/01/ec8aa4ed81aceba6b0ec83b7-2018-01-15-ec98a4ed9b84-6-30-43.png?w=300&h=237"></center>
다른 sota 기술들과 비교해봤을 때 놀라웠던 점은 기존 video action classification 문제에서 대다수의 모델들이 two-stream 기반의 네트워크이며, 입력으로 optical flow를 같이 넣어줬습니다. 이는 모델이 video의 motion 정보를 잘 잡아내지 못하기에 optical flow를 통해 motion 정보를 잘 포착하도록 하는 역활이었습니다. 그런데 non-local block을 사용하니 RGB 입력만으로도 기존 two-stream 방식을 뛰어넘었다는 점이 정말 인상 깊었습니다.

이는 입력으로 들어오는 정보가 적음에도 불구하고 더 좋은 성능을 냈다는 의미이기 때문입니다!!!

> sota 기술들과 비교

<center><img src="https://bloglunit.files.wordpress.com/2018/01/ec8aa4ed81aceba6b0ec83b7-2018-01-15-ec98a4ed9b84-6-36-10.png"></center>
<br>

## Conclusion

non-local neural net은 저자에 들어가 있는 kaiming He의 명성에 걸맞게 정말 엄청난 성능을 보였습니다.

간단한 구조이면서 적용이 쉽고, NLP에서 쓰이던 self-attention을 vision 영역에서 잘 풀어낸 듯한 느낌을 받았고, video task에서 입력의 default값처럼 쓰이던 optical flow를 쓰지 않고도 좋은 성능을 낸 것이 인상적입니다. 

특히나 최근 딥러닝 기술들이 입력 feature로서 hand-crafted feature가 아닌, 원본 그대로 넣거나, 네트워크에서 사람의 개입을 최소화하는 모델들이 좋은 성능을 내는 것을 보아 optical flow처럼 모션정보를 딥러닝 스스로가 학습할 수 있도록 설계된 네트워크를 제안할 수도 있겠다라는 생각이 들었는데, 아니나 다를까 FAIR 그룹에서 CVPR2019에서 그러한 네트워크를 제안했습니다. 

다음 포스트는 SlowFast Net입니다!

<br>

**참고 강의**

[Lunit](https://blog.lunit.io/2018/01/19/non-local-neural-networks/)

[PR-83](https://www.youtube.com/watch?v=ZM153wo3baA)

<br>

