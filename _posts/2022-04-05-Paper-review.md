---
title:  "논문리뷰 - Learning from Imbalanced Data"
toc: true
toc_sticky: true
use_math: true
toc_label: "Contents"
categories: 
  - 논문리뷰
tags:
  - Data Imbalance
  - Imbalance Learning
---

## Learning from Imbalanced Data (2009)
> 해당 포스팅의 이미지는 모두 원 논문 본문에서 발췌하였습니다.
> [논문 링크](https://ieeexplore.ieee.org/document/5128907)

### 1. Introduction

과학기술의 발전으로 인하여 데이터의 축척이 폭발적으로 증가하고 있으며 knowledge discovery, data engineering 등 다양한 분야의 발전으로 이어지고 있다. 하지만 imbalanced data에 의한 imbalanced learning problem이 있다. 이러한 imbalanced learning problem은 일반적 학습 알고리즘 성능을 저해한다는 문제가 있다. 하지만 real world에서는 데이터의 불균형은 직면할 수 밖에 없는 문제이다. <br>이 논문은 아래 세 가지의 큰 틀에서 이야기를 하고 있다.

1. imbalance learning problem 이란
2. 이 문제를 해결하기 위한 방법
3. imbalance learning 시 모델 성능 평가 방법


### 2. Nature of the Problem
Imbalanced data는 각 class간의 데이터 수 비율이 100:1, 1,000:1 등과 같이 하나의 class에 비해 다른 class 데이터가 매우 많은 것을 말하고 있다. 이러한 class간 불균형 (between-class imbalance)은 두 개의 class 사이 (binary), 혹은 두개보다 더 많은 class (multiclass)간에 일어날 수 있다.   


**두 class 간의 불균형 예시: Mammography Dataset**
- Mammography는 유방촬영술이라고 한다. 유방암을 검출하기 위한 검사하는 것을 의미하는 것 같다.
- Mammography를 통해 암에 걸린 환자(positive)를 찾는 경우가 암에 걸리지 않은 환자(negative)를 찾는 경우보다 훨씬 적다. (일반검진 및 약간의 의심이 되는 경우에도 검진을 하게 될것이니.. positive case가 많을 것이라고 생각)
- Mammography dataset에는 260의 "Positive" (minority class, 암환자) 샘플과 10,923의 "Negative" (majority class, 그외) 샘플이 있다. 
- 이러한 imbalance data를 이용하여 예측 모델을 학습을 하는 경우 majority class인 negative 의 경우 분류 성능이 거의 100%에 다다르게 되며 minority class인 positive 의 경우 분류 성능이 0-10%에만 머무르게 된다.
- 이러한 경우 암 환자인 positive의 분류 성능이 낮은 경우는 더욱 값비싼 비용을 치루어야 하는 경우가 생길 수 있으며 이는 큰 비용을 치뤄야 하는 상황이 될 것이다. (암환자를 암이 걸렸다고 예측하지 못했을 때의 비용..) 그렇기 때문에 이러한 경우는 positive이며 minority class인 암환자를 정확하게 예측하는 것이 더욱 도움이 될 것이다.
- 모델 성능 평가 측면에서는 accuracy는 majority class인 negative 샘플의 예측 정확도에 크게 영향을 받기 때문에 receive operating characteristics curves (ROC curve), precision-recall curve (PR curve) 등과 같은 성능 평가 지수를 이용하는 것이 도움이 될 것이다. (Section 4)

**Intrinsic / Extrinsic**   
이 예제와 같이 데이터 공간 특성에 의한 불균형은 *intrinsic* 이라고 한다.   
그 외의 이유에 의한 불균형은 *extrinsic* 이라고 한다.   
(예: 데이터 공간 특성은 imbalance가 아니지만 특정 시간 간격을 두고 저장하다가 생긴 imbalance)   

**Relative imbalance / Imbalance due to rare instances**   
데이터의 imbalance는 *relative imbalance*와 *imbalance due to rare instances* 두가지 경우도 있다. 예를 들어 앞선 mammography dataset에서 두 클래스간의 데이터 차이가 100:1 인 경우,   
1. 총 데이터 수가 200,000인 경우 경우 minority class의 데이터가 2,000개로 majority class에 비해 relative imbalance 하나 절대적인 수는 rare 하지 않다.   
2. Imbalance due to rare instances는 minority class의 데이터의 절대적 양이 극도로 적은 경우를 의미한다.

**Within-class imbalance**   
동일 클래스 내에서 일부 subconcept과 나머지 concept간의 데이터 imbalance를 의미한다. Concept은 2개의 타겟($Target1{\cup}Target2$)을 동일한 class로 두는 것이며, subconcept은 각각의 타겟($Target1$, $Target2$)를 의미하며 이는 concept의 subconcept(subconcepts of concept) 이라고 할 수 있다. (참고: [논문](https://link.springer.com/article/10.1007/s00500-020-04828-5))   

지금까지 나온 개념은 아래의 그림으로 확인할 수 있다.   

![](/imgs/040622_post/fig2.png){: .align-center}
(a)의 경우 between-class imbalance의 예로 원형 데이터가 majority class, 별모양 데이터가 minority class이며 relative imbalance한 것을 알 수 있다. 여기서 minority class의 경우 noise sample이 있는 것을 알 수 있다. Majority class와 minority class 사이에 겹치는 구간이 없는 것을 확인할 수 있다.   
(b)의 경우 A와 B인 majority class와 minority class 사이에 데이터가 겹쳐있는 것을 볼 수 있다. 이는 높은 data complexity를 가진다고 할 수 있다.   
A-D, B-C는 concept과 subconcept간의 관계로 이루어져 있으며 각각의 경우 모두 subconcept이 concept에 비해 imbalance한 데이터를 가지고 있으며 within-class imbalance라고 할 수 있다.   
C의 경우에는 minority class의 subconcept임과 동시에 데이터 수가 매우 적은것을 알 수 있다. 이는 Imbalance due to rare instances의 예라고 볼 수 있다.


**Within-class imbalance**로 인한 모델 성능 저하는 다음과 같이 일어날 수 있다. 분류 모델은 여러 small disjunct (*A small disjunct is a disjunct that covers only a few training examples) 을 아우르며 concept을 분류할 수 있는 규칙을 찾으려 할 것이다. 이때, homogeneous concept인 경우 전반적으로 분류할 수 있는 규칙을 찾아낼 수 있지만 heterogeneous concept의 경우에는 subconcept의 특징을 충분히 찾아내지 못할 것이다.  

**Noise sample**에 의한 문제도 발생할 수 있다. 충분한 데이터를 가지고 있는 majority class에서는 일부 noise sample이 있더라도 noise가 있지 않은 충분한 양의 데이터가 있기 때문에 이러한 문제가 거의 일어나지 않으나, 데이터가 적은 minority class에서는 몇몇의 noise sample에 의해 분류 모델에게 크게 혼동을 주어 class를 분류하기 위한 규칙을 찾는데 있어서 문제가 될 수 있다.   

### 3. The State-of-the-art Solutions for Imbalanced Learning
- 논문 제목에 sota solution이라는 제목을 사용하였지만, 논문이 발간된 지는 너무 오래되었기 때문에 그 당시의 sota 모델 예시라고 볼 수 있다.

...작성중


