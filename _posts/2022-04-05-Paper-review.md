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

#### 3.1.4 Adaptive Synthetic Sampling
앞서 이야기한 SMOTE 알고리즘의 경우 minority 와 majority class간의 경계를 고려하지 않고 데이터를 생성하기 때문에 두 class간 경계를 설정하는 문제에 있어서는 한계가 있을 수 있다고 한다. 이러한 문제에 대응하기 위해 *Borderline-SMOTE*와 *Adaptive Synthetic Sampling (ADA-SYN)*이 제시되었다.   

***Borderline-SMOTE***
각각의 minority class 샘플로부터 가장 가까운 근처 (앞서부터 계속 nearest neighbors의 개념을 사용중) 샘플 m개 중 majority class 샘플의 개수 $N_{k-maj}$를 계산한다. 계산된 값을 이용하여 아래와 같이 경우를 나누어 알고리즘을 수행한다.
1. $m/2 \leq N_{k-maj} < m$ 인 경우, "DANGER" 로 분류한다. DANGER로 분류된 샘플들을 이용하여 SMOTE를 수행하게 된다. 
2. $N_{k-maj} = m$ 인 경우, "NOISE" 로 분류한다. 이러한 경우는 SMOTE를 수행하지 않는다.

아래 그림을 보면 더 확실하게 이해할 수 있다.
![](/imgs/040622_post/fig4.png){: .align-center}

Minority class의 샘플 중 majority class 샘플과 인접한, 즉 경계선에 있는 샘플들에 대해서 생성을 진행하는 것을 알 수 있다.


***ADASYN***

1. 생성할 샘플의 수를 다음과 같이 결정하게 된다. Majority class의 샘플 수 와 minority class의 샘플 수 차이에 $\beta$를 곱한 수를 선택하게 된다.
$G=(|S_{maj}|-|S_{min}|)\times\beta$   
$|S_{maj}|$: Majority class의 샘플 수, $|S_{min}|$: Minority class의 샘플 수, $\beta\in[0,1]$   
2. Minority class 각각의 샘플 $x_i \in S_{min}$에 대해서 K개의 가까운 샘플을 찾고 아래와 같이 $\Gamma_i$ 값을 구한다. 
$\Gamma_i=\frac{\Delta_i/K}{Z}, i=1,...,|S_{min}|$   
$\Delta_i: x_i$로부터 가장 가까운 k개의 샘플 중 majority class 샘플의 수, $Z:$ Normalization 상수, $\sum\Gamma_i=1$이 되도록 설정   
$\Gamma_i$는 각 샘플당 생성되어야 할 생성 샘플 비율이라고 보면 된다.
3. 앞서 각 샘플당 생성되어야 할 생성 샘플 비율을 설정하였으니 각 샘플 당 생성되어야 할 샘플 수($g_i$)를 아래와 같이 계산하게 된다.   
$g_i=\Gamma_i\times G$   
이 후에 생성은 논문에는 설명되어 있지 않으나 아마 앞서 소개된 SMOTE를 사용할 수 있지 않을까 싶다.


- *Borderline-SMOTE*의 경우 특정 조건에 맞는 샘플에 대해서 균일하게 데이터를 생성하게 하였다면 (주로 major, minority class가 인접해 있는 경계 부분), ADASYN은 경계 부분에서도 majority class와 더욱 더 인접해 있는 샘플에 가중치를 주어 더 많은 데이터를 생성하게 된다.

#### 3.1.5 Sampling with Data Cleaning Techniques
이번에는 앞서 소개된 샘플링 기법을 통해 얻어진 데이터에 대해서 중복된 뎉이터를 제거하는 기법인 Tomek links 에 대해서 소개하고 있다.   
Tomek links란 가장 가까우면서 각각의 샘플이 다른 클래스에 속해 있는 샘플 쌍을 의미한다. 이러한 샘플 쌍 $(x_i, x_j)$은 다음과 같이 정의가 된다.
$(x_i, x_j), x_i \in S_{min}, x_j \in S_{maj}$
두 샘플 간 거리는 $d(x_i, x_j)$ 로 한다.

여기서 $(x_i, x_j)$는 아래의 조건을 만족할 때 Tomek link라 한다.
- $d(x_i, x_k) < d(x_i, x_j)$ 또는 $d(x_k, x_j) < d(x_i, x_j)$를 만족하는 $x_k$가 존재하지 않는 경우
이와같이 정의가 된 Tomek link는 둘 중 하나의 요소가 노이즈 이거나 두 요소가 경계에 있는 요소라고 생각할 수 있다. 앞서 제시된 데이터 생성 후 데이터 클리닝을 위해 Tomek links를 "cleanup" 하는 방법을 취할 수 있다. 이러한 예제는 그림으로 보면 이해가 더 쉽다.

![](/imgs/040622_post/fig5.png){: .align-center}
위 그림에서 (a)는 원 데이터 분포를 보여주고 있다. (b)는 SMOTE를 통해 minority class인 별 모양 데이터가 추가된 것을 볼 수 있다. 이때 majority class와 minority class 사이에 혼잡하게 섞여있는 형태를 볼 수 있다. (c) Tomek links로 확인된 데이터를 볼 수 있다. (d) Tomek links로 확인덴 데이터를 "cleanup" 한 후의 결과이다.

위처럼 SMOTE를 진행한 후 중복(overlap) 되는 데이터가 많아진 것을 확인할 수 있다. Tomek links를 확인하여 데이터를 더 깔끔하게 만들 수 있는 것을 볼 수있다. (개인적인 생각으로는 애초에 원 데이터셋이 너무 경계가 뚜렷하지 않은 좋지 않은 예이지 않나 싶긴 하다..)

#### 3.1.6 Cluster-Based Sampling Method
클러스터링 기반 샘플링 방법은 이전 방법들과는 다르게 특정 타겟에 맞추어 샘플링을 할 수 있는 기법이다. 여기서는 cluster-based oversampling (CBO) algorithm 이 소개되고 있다.   
CBO 알고리즘은 K-means clustering 기법을 기반으로 한다.   
각각의 class에서 K 개의 샘플을 랜덤하게 샘플링한 후 샘플된 데이터의 feature에 대해서 평균 값을 구하여 mean feature vector를 구하게 된다. 이러한 mean feature vector는 샘플링 된 데이터 클러스터의 중앙을 가리키는 벡터가 될 것이다.   
선택되지 않은 샘플 중 1개에 대해서 각 cluster의 mean feature vector와의 euclidian distance를 구하게 된다. 각 class 중 distance가 가장 가까운 cluster 로 샘플을 추가하게 되고 해당 cluster의 mean feature vector를 다시 계산하게 된다.   
이러한 방식으로 선택되지 않은 샘플 모두에 대해서 진행하게 된다. 이때 샘플이 가장 많은 majority class와 샘플 수가 동일하게 되도록 minority class에 대해서 oversampling 을 진행하게 된다. 아래 그림으로 내용을 확인해보자.

![](/imgs/040622_post/fig6.png){: .align-center}
(a)는 원 데이터의 분포이다. (b)는 각 class에서 K=3 개의 샘플을 샘플링한 후 mean feature vector가 가리키는 위치 (삼각형, clustering mean)을 보여주고 있으며 샘플된 포인트와 각 cluster mean의 distance를 보여주고 있다. (c)는 새로 계산되는 cluster mean을 보여주고 있다. (d)는 minority class 인 D, E에 대해 cluster-based oversampling 을 진행한 후의 모습이다. 여기서 oversampling을 하는 경우 앞서 소개된 데이터 생성 예제와 같은 방법을 사용하여 진행한다. 이러한 방법은 within-class imbalance와 between-class imbalance에 효과적이라고 한다.

이 방법 같은 경우는 개인적으로는 효과적인 것인가에 대한 의문점이 든다.. 

#### 3.1.7 Integration of Sampling and Boosting
(작성 중...)