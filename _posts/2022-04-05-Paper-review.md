---
title:  "Paper review - Learning from Imbalanced Data"
toc: true
toc_sticky: true
use_math: true
toc_label: "Contents"
categories: 
  - Paper review
tags:
  - Data Imbalance
  - Imbalance Learning
---

# Learning from Imbalanced Data (2009)
> 해당 포스팅의 이미지는 모두 원 논문 본문에서 발췌하였습니다.
> [논문 링크](https://ieeexplore.ieee.org/document/5128907)

## 1. Introduction

과학기술의 발전으로 인하여 데이터의 축척이 폭발적으로 증가하고 있으며 knowledge discovery, data engineering 등 다양한 분야의 발전으로 이어지고 있다. 하지만 imbalanced data에 의한 imbalanced learning problem이 있다. 이러한 imbalanced learning problem은 일반적 학습 알고리즘 성능을 저해한다는 문제가 있다. 하지만 real world에서는 데이터의 불균형은 직면할 수 밖에 없는 문제이다. <br>이 논문은 아래 세 가지의 큰 틀에서 이야기를 하고 있다.

1. imbalance learning problem 이란
2. 이 문제를 해결하기 위한 방법
3. imbalance learning 시 모델 성능 평가 방법


## 2. Nature of the Problem
- 이번 섹션에서는 Imbalance data에 대한 정의와 추가적인 개념을 함께 설명하고 있다.

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

## 3. The State-of-the-art Solutions for Imbalanced Learning
- 이번 섹션에서는 Imbalanced learning에 의한 문제를 극복하기 위한 방법들을 소개하고 있다.
- 논문 제목에 sota solution이라는 제목을 사용하였지만, 논문이 발간된 지는 너무 오래되었기 때문에 그 당시의 sota 모델 예시라고 볼 수 있다.

### 3.1 Sampling Methods for Imbalanced Learning
데이터 샘플링을 통하여 데이터 분포를 균일하게 맞추어 주는 방식을 말한다. 많은 경우에 샘플링을 통해 데이터 수의 균형을 맞춘 결과 더 좋은 모델 성능을 보였다고 이야기하고 있다.

#### 3.1.1 Random Oversampling and Undersampling
*Random oversampling*이란 minority class의 데이터셋 $S_{min}$으로 부터 랜덤하게 데이터를 샘플링하여 $S_{min}$에 데이터를 추가하게 된다. 즉, 샘플되어 추가된 데이터는 중복되어 있는 상태이다. 이러한 방식은 중복된 minority class 데이터로 인하여 분류 모델 학습 시 overfitting이 일어날 수 있다.
*Random unversampling*이란 majority class의 데이터셋 $S_{maj}$으로 부터 랜덤하게 데이터를 제거하는 방식을 말한다. 이러한 방식은 제거된 데이터로부터 얻을 수 있는 중요한 특징을 분류 모델이 얻게되지 못할 수 있다는 문제가 있다.

#### 3.1.2 Informed Undersampling
여기서는 *EasyEnsemble*, *BalanceCascade*, *undersampling using kNN* 세가지 방법을 소개한다.   
*<b>EasyEnsemble</b>*은 majority class의 데이터를 undersampling 하게 되는데 이때 하나가 아닌 여러개의 undersampled majority class 데이터셋을 만들게 된다. 각각의 undersampled majority class 데이터셋과 하나의 minority class 샘플을 이용하여 여러개의 분류 모델을 이용하여 학습하게 된다. 여기서 undersampled majority class 데이터의 경우 여러개의 서브셋으로 구성되어 있고, 하나의 분류모델을 학습 시 서브셋 중 하나를 활용하게 된다. 이렇게 되면 여러개의 모델은 각각의 undersampled majority class를 통해 구성된 서브셋과 minority class 데이터셋을 모델 학습에 이용하게 되기 떄문에 각기 다른 데이터셋을 이용하여 학습하게 된다. 최종 예측 결과는 여러개의 분류 모델의 예측 결과를 종합하는 앙상블 방식을 이용하게 된다.   

*<b>BalanceCascade</b>*란 majority class $S_{maj}$로부터 undersample 된 데이터셋 $E$와 minority class의 데이터셋 $S_{min}$을 이용하여 분류 모델을 학습하게 된다. 학습된 모델을 이용하여 $E$를 분류하고 모델이 제대로 분류한 샘플을 $N_{maj}^{\ast}$ 라고 한다면 해당 데이터를 표현해 줄 수 있는 데이터는 중복되어 있다고 생각하여 $S_{maj}$로부터 제거하게 된다. 이와 같은 작업을 반복적으로 수행하게 되어 데이터를 스크리닝(?) 하게 된다.   

*<b>Undersampling using K-nearest neighbor (KNN)</b>*은 KNN을 이용하여 undersampling 하는 기법이다. 여기에는 NearMiss-1, NearMiss-2, NearMiss-3 등이 있다. NearMiss-1 의 경우 각각의 majority 샘플에 대해 <b>가장 가까운</b> 3개의 minority sample 까지의 거리의 평균이 작은 순으로 선택한다. NearMiss-2의 경우 각각의 majority 샘플에 대해 <b>가장 먼</b> 3개의 minority sample 까지의 거리의 평균이 작은 순으로 선택한다. NearMiss-3의 경우 각 minority 샘플이 가장 가까운 majority sample을 선택한다.

#### 3.1.3 Synthetic Sampling with Data Generation (SMOTE)
*SMOTE*란 minority class의 새로운 데이터를 존재하는 데이터를 기반으로 만들어내는 방식이다. Minority class의 샘플 중 하나를 선택하고 같은 class 의 샘플 중 가장 가까운 K개의 샘플 중 1개를 선택하게 된다. 이 두 샘플 사이에 새로운 데이터를 추가하는 방식이다. 데이터 추가는 아래와 같이 된다.
<center>$x_{new}=x_{i}+({\hat{x}}_{i}-x_i)\times\delta$</center>
$x_{new}$: 새로 추가된 데이터, $x_i$: minority class 샘플 데이터, $\hat{x}_{i}$: $x_i$ 샘플에서 가장 가까운 k개의 minority 샘플 중 1개, $\delta$: [0, 1] 중 랜덤 수

SMOTE를 그림으로 살펴보자.
![](/imgs/040622_post/fig3.png){: .align-center}

위 그림에서 (a)의 경우 minority class(별모양)의 샘플 $x_i$와 그 주위에 가장 가까운 6개의 샘플 이 선으로 연결되어 있는 것을 볼 수 있다. (b)의 경우 6개 중 아래에 있는 $\hat{x}_i$와 $x_i$ 사이에 데이터를 생성하는 예시를 보여주고 있다.
