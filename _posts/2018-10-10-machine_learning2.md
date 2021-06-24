---
layout: post
title: Ensemble
tags: [ML]
---

# Ensemble(앙상블)이란?

Ensemble이란, 여러개의 모델을 학습, 결과를 합쳐서 예측하는 machine learning 방법입니다. 

- 약한 학습기를 여러개 사용, 강한 학습기를 만드는 방식

* 다양한 모델의 결과를 종합, 전반적인 오류를 줄임

* 각 학습기 별로 bias(편향)이 존재해 다양한 bias를 종합하여 생성하며, 이로 인해 overfitting(과적합)을 방지함

  > Ensemble 모델 예시

<center><img src="https://user-images.githubusercontent.com/31475037/59009627-4ad61b00-8869-11e9-97b0-beab5af48dbb.png" width="90%"></center>
<br>

## Bagging과 Pasting

Ensemble기법에서 많이 쓰이는 bagging과 pasting에 대해 먼저 알아봅시다.

### Bagging

* Bootstrap aggregating의 줄임말
* 학습 데이터셋에서 중복을 허용하여 sampling
* 통계학에서는 중복을 허용한 resampling을 bootstraping이라고함
* 예측 모형의 변동성이 큰 경우 예측 모형의 변동성을 감소시키기 위해 쓰임
* 여러번의 resampling을 통해 예측 모형의 분산을 줄여 줌으로써 예측력을 향상

### Pasting

* 중복을 허용하지 않고 sampling하는 기법

<br>

## Voting

Ensemble의 다양한 기법중 voting이라는 방법이 존재합니다. Voting은 크게 두가지로 나뉩니다.

### Hard Voting

학습한 모델을 이용하여 새로운 데이터에대해 예측을 하고 각 분류기의 예측을 모아서 가장 많이 선택된 클래스를 예측하는 방식입니다.

### Soft Voting

모든 분류기의 클래스의 확률을 예측함, 분류기가 클래스별로 예측한 확률을 평균을 내어 확률이 가장 높은 클래스를 예측하는 방식입니다.

확률이 높은 투표에 비중을 더 두기에, hard voting보다 성능이 높습니다.

<br>

## Boosting

성능이 약한 학습기를 여러개 연결하여 강한 학습기를 만드는 방식입니다.

### Ada Boost

틀린 샘플의 가중치를 높여 새로 학습된 모델이 학습하기 어려운 데이터에 더 적합하도록 훈련시킵니다.

> Ada Boost의 방법론

<center><img src="https://user-images.githubusercontent.com/31475037/59010815-8541b700-886d-11e9-9b09-e5e341f6021e.png" width="90%"></center>
### Viola-Jones face detector(viola 2004)

Ada boosting을 이용한 대표적인 기술은 Viola-Jones face detector 기술입니다. 그 때 당시, 얼굴 검출기 모델에서의 사실상의 표준 기술이었습니다. Ada boosting과 cascade 분류기 그리고 harr-like feature를 사용한 것이 특징입니다.

* Harr-like feature 사용
  * 검은 영역의 합에서 흰 영역의 합을 뺌으로서 얼굴을 검출함

<center><img src="data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAYQAAACCCAMAAABxTU9IAAAAWlBMVEX///8AAAB5eXnu7u63t7c1NTXa2trHx8d1dXWoqKji4uIaGhr7+/taWlre3t4QEBDp6ekICAj09PRFRUUiIiK+vr7Q0NBsbGyioqJTU1MtLS1AQEDFxcVJSUlsgiY/AAAC2klEQVR4nO3cW06UURREYRRFbLGFVi7e5j9Nk10+kEgbiiroo72+AZzs+ldI+omTEwAA7tm8WdTNob/MC7p7tajbQ3+ZF/T+0B97n/N82/b89cIujyPCj0Nv+KuzPyNcxco3FiK8K5/U9UCE6802srn50L3xGCNs08U7IngeiHC2f8vjnK4a4fv2MtadJs/xl7BshM+7/KXuNCGCqTtNiGDqThMimLrThAim7jQhgqk7TYhg6k4TIpi604QIpu40IYKpO02IYOpOEyKYutOECKbuNCGCqTtNiGDqThMimLrThAim7jQhgqk7TYhg6k4TIpi604QIpu40IYKpO02IYOpOEyKYutOECKbuNCGCqTtNiGDqThMimLrThAim7jQhgqk7TYhg6k4TIpi604QIpu40IYKpO02IYOpOEyKYutOECKbuNCGCqTtNiGDqThMimLrThAim7jQhgqk7TYhg6k4TIpi604QIpu40IYKpO02IYOpOEyKYutOECKbuNCGCqTtNiGDqThMimLrT5Kgi/MwfIsLT/f6XnJ9y3WlyXBEWRYQFEGEBRFgAEZ4kv2jsvsxrRHiS/KJxSYRAftEgQiK/aBAhkV80iJDILxpESOQXDSIk8osGERL5RYMIifyiQYREftEgQiK/aBAhkV80iJDILxpESOQXDSIk8osGERL5RYMIifyiQYREftEgQiK/aBAhkV80iJDILxpESOQXDSIk8osGERL5RYMIifyiQYREftEgQiK/aBAhkV80iJDILxpESOQXDSIk8osGERL5RYMIifyiQYREftEgQiK/aBAhkV80iJDILxpESOQXDSIk8osGERL5RYMIifyiQYREftEgQiK/aBAhkV80iJDILxrPGGFHhEfaH+F6s83c/P8RLjr2R1jOghE+dny9mteIsAAiLOBehLtD37LP7RFF2G03S9peFiJ8e7uw+Pfov+HidGWH/joAAAAr+gVQjaTkW/0xpgAAAABJRU5ErkJggg==" width="50%"></center>
* cascade(직렬) 분류기 사용
  * Harr-like feature로 추출된 영역(window)의 feature를 약한 모델들에 차례대로 넣고, 얼굴 가능성이 없는 영역은 조기에 기각 시킴
  * 모든 검출기를 거치고 나면 해당 영역이 얼굴이라 판별
  
  > Viola-Jones face detector 모델

<center><img src="https://user-images.githubusercontent.com/31475037/59011062-609a0f00-886e-11e9-8322-d785202a9afa.png" width="90%"></center>
### Bagging과 Boosting 비교

<center><img src="https://user-images.githubusercontent.com/31475037/59011343-5a586280-886f-11e9-86ab-3143ad265195.png" width="90%"></center>
<br>

### Stacking

Stacking은 각 모델의 예측값을 가지고 새로운 메타 모델을 학습시켜 최종 예측모델을 만드는 방식을 일컫습니다.

예를들어 3개의 예측기의 각기 다른 값을 가지고 마지막 예측기(blender, 메타 학습기)가 이 예측을 입력으로 받아 최종 예측을 하도록 만듭니다.

> Stacking의 예시

<center><img src="https://user-images.githubusercontent.com/31475037/59014269-300aa300-8877-11e9-93ac-1f20996f4101.png" width="90%"></center>
<br>