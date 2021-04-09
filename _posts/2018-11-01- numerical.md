---
layout: post
title: 수치미분과 해석미분 그리고 그래디언트
tags: [Math]
---

# 미분에 대한 간략한 설명

당신이 마라톤 선수고 처음부터 10분에 2km씩 달렸다고 가정해봅시다.

이 때 속도는 2/10 = 0.2(km/분)입니다.

<span style="color:red">달린거리</span>가 <span style="color:blue">시간</span>에 대해 얼마나 변화 했는가를 계산했습니다.

그런데 10분이라는 시간을 특정 순간까지 줄인다면?

미분은 이런 **특정순간**의 변화량을 의미합니다.

X의 **작은 변화**가 함수 f(x)를 얼마나 변화 시키냐는 의미입니다.

즉, 함수에서 미분의 개념이 기울기라는 것을 의미합니다.

> 미분 공식

<center><img src="https://user-images.githubusercontent.com/31475037/59031273-2b0d1a00-889e-11e9-89bd-77c24644d0cc.png" width="40%"></center>
여기서 미분 방식이 크게 두 갈래로 나뉩니다.

**해석 미분(Analytical Differentiation)**

해석적 미분 방식은 종이와 펜을 이용해 논리적인 전개로 풀 수 있는 문제를 말합니다.

**수치 미분(Numerical Differentiation)**

수치적 미분은 해석적 미분 방식으로는 풀 수 없는 문제가 있을 때 수치적 접근을 통해 근사 값을 찾는 방식입니다.

<br>

### 수치 미분(numerical differentiation)

수치 미분은 현실세계에 미분을 적용하기 위한 실용적인 학문으로 수치해석과목에서 자세하게 배울 수 있습니다.

수치 미분은 아주 작은 차분으로 미분함으로써, 미분 공식과 근사적으로 가까운 결과를 낼 수 있게 해줍니다.

예를들어 변화량 dx를 dx = 0.000001로 놓고 미분값을 구하는 것입니다.

> 파이썬에서의 수치 미분 구현

파이썬에서 구현을 할 시 주의해야할 사항은 변화량인 dx(h) 변수값을 소숫점 8자리 이하로 내리면 안된다는것입니다.

그렇게 되면 반올림 오차 문제로 작은값이 생략되어 잘못된 계산결과가 도출되기 때문입니다.

다음과 같이 중앙 차분을 이용해 구현을 해야 됩니다.

<center><img src="https://user-images.githubusercontent.com/31475037/59032086-63155c80-88a0-11e9-8fc6-454a7f889adf.png"></center>
<br>

> 중앙 차분

중앙 차분이란 수치미분의 한 종류로, 다른 종류인 전향차분 후향차분보다 더 정확하며, 대다수의 경우 중앙차분을 씁니다.

<center><img src="https://user-images.githubusercontent.com/31475037/59031364-6c052e80-889e-11e9-88b3-8cae50e7ac7d.jpg" width="80%"></center>
<br>

### 편미분

편미분은 변수가 여러개일 때 어떻게 미분을 해야하는가와 각 변수가 함수에 얼마나 영향을 미치는가를 어떻게 구하는가에 대한 방법입니다.

다음과 같이 미분하고 싶은 하나의 변수만 집중하고, 나머진 상수취급해버립니다.

<center><img src="https://user-images.githubusercontent.com/31475037/59032468-59402900-88a1-11e9-8029-b0e8b31a1ea4.png" width="30%"></center>
<br>

### gradient

gradient는 입력이 벡터일 때 결과가 스칼라인 함수에 대해서 입력 벡터의 각 원소 마다 편미분을 해 결과를 벡터로 내놓는 방식입니다.

즉, gradient는 공간에 대한 기울기(slope)를 말합니다.

> gradient 표현 

<center><img src="https://wikimedia.org/api/rest_v1/media/math/render/svg/832275c31fbf4723a0ce1df6ff0611bf264ab8eb"></center>
그럼 간단한 예시를 풀어볼까요?

> gradient  예시

$$
f(x,y) = x^2 + y^2일 \ 때
$$

$$
\nabla f = (\frac {\partial f} {\partial x}, \frac {\partial f} {\partial y}) = (2x, 2y)
$$



각 점에서의 기울기를 계산하고, 기울기 결과에 마이너스를 곱한 벡터(기울기의 반대방향)로 그림을 그려보면 다음과 같습니다.

> gradient의 표현

<center><img src="https://user-images.githubusercontent.com/31475037/59031365-6c052e80-889e-11e9-8b51-e8d7b111d797.png"></center>
기울기의 반대방향이 가르키는 방향이 각 지점에서 함수의 출력 값을 가장 크게 줄이는 방향입니다. (The direction of steepest descent is the negative gradient!)

multiple dimension 벡터에서 gradient를 구하면, 각 dimension마다 gradient가 추출됩니다.

<br>

**읽어볼만한 글**

[미분과 gradient](https://ghebook.blogspot.com/2010/07/gradient.html)

[스칼라장의 기울기](https://wikidocs.net/6998)

[numerical differntiation 코드](https://github.com/chacha95/Deeplearning_example/blob/master/01.numerical_differntiation.ipynb)



