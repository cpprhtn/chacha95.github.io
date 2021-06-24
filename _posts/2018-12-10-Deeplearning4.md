---
layout: post
title: Activation Function
tags: [ML]
use_math: true
---

이전 layer의 모든 입력에 대한 가중 합을 취하고 출력 값(일반적으로 비선형)을 생성하여 다음 레이어로 전달하는 함수입니다.

각 뉴런의 입력이 모델의 예측과 관련되어 있는지 여부에 따라 활성화 됩니다. 이런 활성화를 통해 신경망은 입력값에서 필요한 정보를 학습합니다.

activation function은 대게 non-linear function을 씁니다. 활성화 함수로 linear function을 쓰지 않는 이유는 다음과 같습니다.

선형함수인 h(x) = cx를 활성화 함수로 사용한 3층 네트워크를 생각해보세요. 이를 식으로 나타내면 y(x) = h(h(x))가 됩니다. 이는 실은 y(x) = ax와 똑같은 식입니다. a = c^3이라고만 하면 끝이죠. 즉, hidden layer가 없는 네트워크로 표현할 수 있습니다. 

또한 선형함수의 미분값은 상수값이기에 입력값과 상관 없는 결과를 가집니다.

<br>

## Sigmoid

로지스틱 함수로도 불립니다. 

<center><img src="https://user-images.githubusercontent.com/31475037/59671599-ec118980-91f8-11e9-9e1c-51d1b6fc101e.png" width="50%"></center>
**시그모이드 특징**

- 함수의 결과 값이 [0, 1] 범위
- 중간값은 0.5
- 매우 큰 값을 가지면 함수값은 거의 1이며 매우 작은 값을 가지면 거의 0이다



**문제점**

- <span style="color:red">zero-centered 되있지 않음</span>

  만약 모든 x값들이 같은 부호라고 가정할 시, 한 노드에 대해 모든 파라미터 w의 미분값은 모두 같은 부호를 같게된다. 따라서 같은 방향으로 update 되는데 이러한 과정은 학습을 zigzag 형태로 만들어 느리게 만드는 원인이 됨

- <span style="color:red">Gradient vanishing</span> 

  미분함수에 대해 x=0에서 최댓값 0.25를 가집니다. 

  입력 값이 일정이상 올라가면 미분값이 거의 0에 수렴하게 됩니다. 

  x의 절대 값이 커질 수록 gradient backpropagation시 미분 값이 소실 될 가능성이 큼(Saturated regime이 gradient를 죽임)

  > sigmoid의 도함수, x = 0에서 최대 값을 가진다.

  <center><img src="data:image/png;base64,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"></center>

- <span style="color:red">exp 함수 사용시 계산 비용이 큼</span>



> Saturate 영역에 있을시 Gradient가 죽는다

<center><img src="https://user-images.githubusercontent.com/31475037/59671596-eb78f300-91f8-11e9-9e55-1ac74fad0eac.png" width="80%"></center>
> x=-10, x=0, x=10 에서 gradient가 어떻게 되는지 생각해보자!

<center><img src="https://user-images.githubusercontent.com/31475037/59671600-ec118980-91f8-11e9-9c33-3f2bdb64e67f.png"></center>
> zero-centerd 되있지 않아서 weight 업데이트가 느리다.

이상적으로 weight가 업데이트 된다면 아래 그림의 파란색 선과 같이 업데이트 되야 하나, zero-centerd 되 있지 않으면, weight가 빨간색과 같이 zig-zag로 업데이트됩니다.

<center><img src="https://user-images.githubusercontent.com/31475037/59671601-ec118980-91f8-11e9-90d3-acf6eed9bbce.png"></center>
<br>

## Tanh

zero-centered 되있고, 함수의 결과값이 [-1, 1]임

하지만 미분 함수에 대해 일정 값 이사 커질 시 미분값이 소실되는 gradient vanishing 문제는 여진히 남아 있다.

<center><img src="https://user-images.githubusercontent.com/31475037/59671602-ec118980-91f8-11e9-9236-0316f2d4a6b6.png"></center>
<br>

## ReLU

최근 가장 많이 사용되는 활성화 함수입니다.

다음과 같은 특징을 가집니다.

- 계산 효율이 좋음

- x > 0 이면 기울기가 1인 직선이고 , x< 0이면 함수값이 0이 됨

- sigmoid, tanh 보다 학습이 더 잘 됨(gradient vanishing문제가 덜 일어남)

- x < 0 인 값들에 대해서는 기울기가 0이기 때문에 gradient가 죽어버리는 일이 발생하기도 함

<center><img src="https://user-images.githubusercontent.com/31475037/59671604-ecaa2000-91f8-11e9-9fe5-bb724fca7688.png" ></center>
> Sigmoid나 Tanh 보다 Gradient가 죽는 일은 많이 줄었으나 x < 0인 구간에서 gradient가 죽음

<center><img src="https://user-images.githubusercontent.com/31475037/59671605-ecaa2000-91f8-11e9-8b69-6c46b8ab6c73.png"></center>
<br>

## Leaky ReLU

Relu에서 x< 0 에서 gradient vanishing 문제를 해결하기 위해서 만들어 졌습니다.

x < 0인 부분에서도 약간의 값을 줘 gradient vanishing 문제를 해결하였습니다.

<center><img src="https://user-images.githubusercontent.com/31475037/59671608-ed42b680-91f8-11e9-8463-d4b0aa56427c.png"></center>
<br>

## PReLU

새로운 파라미터 a를 추가혀여 x < 0 에서 기울기를 학습할 수 있게 만들었습니다.

<center><img src="https://user-images.githubusercontent.com/31475037/59671609-eddb4d00-91f8-11e9-9533-2d5170d59db8.png"></center>
<br>

## Max Out

연결된 두개의 뉴런 값 중 큰 값을 취하는 방식입니다. ReLU를 일반화 시켜 w1, w2, b1, b2가 학습이 되도록 파라미터를 추가합니다.

계산량을 많이 먹어 자주는 쓰이지 않는 방식입니다.

<center><img src="https://user-images.githubusercontent.com/31475037/59671610-eddb4d00-91f8-11e9-909d-376a763c87eb.png"></center>
<center><img src=""></center>
<br>

## 사용 가이드라인

- 우선 가장 많이 사용되는 함수는 ReLU
- sigmoid의 경우에는 사용하는것을 추천하지 않음
- Sigmoid는 쓰지 말라고 권장하지만 LSTM 같은 모델에서 씀
- tanh의 경우에도 좋은 성능 보장 힘듬

<br>

**참고 강의**

[CS231n](https://www.youtube.com/playlist?list=PL3FW7Lu3i5JvHM8ljYj-zLfQRF3EO8sYv)

<br>



