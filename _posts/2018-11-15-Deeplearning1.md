---
layout: post
title: Linear Classification
tags: [ML]
---

# Linear Classification이란?

Linear Classification(선형 분류)란 쉽게 말해서 선을 이용하여 집단을 두개 이상으로 분류하는 모델입니다. 

이런 선을 단 한개 사용해서 두개의 class로 분류 할 경우 binary classification이라고 부릅니다.

> multinomial classfication

하지만 실제 세상에서는 binary하게 분류하는 문제는 거의 없고 입력값에 대해 여러 class 중 하나를 택해 분류 하는 문제가 대다수입니다.

아래 그림은 3가지 클래스에 대해 linear classification한 예시입니다.

<center><img src="https://user-images.githubusercontent.com/31475037/59895096-332c9400-941e-11e9-972d-860e918152cb.png"></center>
<br>

## Linear Classifier

그렇다면 Linear Classifier는 어떻게 작동 할까요?

정말 간단한 Linear Classifier를 하나 생각해 봅시다.

RGB 컬러 이미지의 차원은 32x32x3(width x height x channel)입니다. 이 3차원 shape를 가진 이미지를 1차원의 shape를 가진 모양으로 바꿔주면 3072차원의 벡터가 나옵니다.

3072차원의 벡터에 Linear Classifier의 Weight matrix와 matrix multiplication을 해주면, 우리가 분류해야 할 class 만큼의 결과 값이 나옵니다. 여기서 class의 총 개수가 10개 이므로, Wx의 결과값은 10개가 나오게 되고, 여기에 bias 텀을 더해주게 되면, Classifier가 예측한 값이 나오게 됩니다.

![](https://user-images.githubusercontent.com/31475037/59840463-5c511400-938d-11e9-9a9b-b4af352a0067.png)

이를 정말 간략하게 표현하면 다음과 같이 나타낼 수 있습니다. 

이미지를 strecth(3차원 shape를 1차원 shape로)한 뒤, Weight matrix와 곱한 뒤 bias를 더해 나온 최종 score로 표현해 줍니다. 



![](https://user-images.githubusercontent.com/31475037/59840464-5ce9aa80-938d-11e9-8ce3-faf28fb0812a.png)

Classifier가 내놓은 결과 값에 대해 제대로 분류를 했나 평가를 하기 위해서, 정답 레이블과 비교를 합니다. 비교를 해주는 함수가 바로 Loss 함수 입니다.

아래 그림에서 처럼 y라는 정답 레이블이 있고, 해당 정답 레이블에는 정답이면 1 아니면 0인 값이 적혀 있습니다.

예를 들어 cat car frog 이 세개 class에 대해 입력 이미지가 cat이라면 y 레이블이 [1, 0, 0]으로 구성되어 있겠죠?

![](https://user-images.githubusercontent.com/31475037/59840465-5ce9aa80-938d-11e9-8bc5-7d7da97cb58a.png)

이처럼 Loss 함수를 기반으로 모델이 잘 분류를 했나 안했나 판단할 수 있습니다.

일반적인 Loss 함수의 구성은 다음과 같습니다.

Loss term과 Regularization term으로 구성되어 있으며, Regularization term은 generalization 능력을 키워 모델이 학습 중에 Overfitting되지 않게 해줍니다.

(Regularization의 자세한 내용은 Training 포스트에서 다루겠습니다.)

<center><img src="https://user-images.githubusercontent.com/31475037/59832157-3cfeba80-937e-11e9-8862-aaff57cff62f.png"></center>
그렇다면 이런 Loss 함수를 기반으로 어떻게 Weight와 bias를 업데이트 할 까요?

그건 다음 포스트에서... 

<br>

**참고 강의**

[CS231n](https://www.youtube.com/playlist?list=PL3FW7Lu3i5JvHM8ljYj-zLfQRF3EO8sYv)

<br>

