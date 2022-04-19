---
title:  "Algorithm study - Floyd's cycle-finding algorithm"
toc: true
toc_sticky: true
use_math: true
toc_label: "Contents"
categories: 
  - Algorithm
tags:
  - Algorithm
  - Floyd
  - Linked-list
  - Cycle-finding
---


# Floyd's Tortoise and Hare
Floyd's Tortoise and Hare은 linked-list로 연결되어 있는 데이터 구조에서 사이클이 시작되는 부분을 효율적으로 찾게 해주는 알고리즘이다.   

## 무지해서 필요한 background
일단 이를 이해하기 위해서는 모듈러에 대해서 잠시 알아보자.   
어떤 양의 정수 n과 a가 주어졌을 때, 아래와 같이 몫 q와 나머지 r을 얻을 수 있다.   
$a = qn + r, 0 <= r < n$   
이것을 아래처럼 표현할 수 있다.
$a \equiv r mod n$   
이것을 풀어서 생각해보면, a를 n으로 나누었을 때 나머지는 r이다 라고 생각하면 될 것 같다.


## Cycle-finding algorithm
일단 linked-list에 cycle이 있는지를 찾기 위해서 Floyd's cycle-finding algorithm을 사용할 수 있다.   
두개의 포인터를 두고 iteration 마다 하나는 1 step 씩, 나머지 하나는 2 step 씩 다음 list로 넘어가게 한다. 두 리스트간의 속도 차이를 이용하는 방식이다.   
어떤 linked-list가 아래와 같은 조건으로 있다고 가정하자.
- Noncycle node는 -F ~ -1 까지 존재
- Cycle node는 0 ~ C-1 까지 존재 (총 개수는 C개)
- Noncycle node -1 은 cycle node 0을 가리키고 있음  
- Cycle node 0은 cycle node C-1 가 가리키고 있음

포인터 1 (P1)은 매 스탭마다 1 step씩 움직이는 느린 친구,   
포인터 2 (P2)는 매 스탭마다 2 step씩 움직이는 빠른 친구라 가정   

F iteration 이후에는 P1은 node 0을, P2는 cycle node 에서 어떤 node h를 가리키고 있다.
이때 P2의 현재 외치를 아래와 같이 생각할 수 있다.
$F \equiv h (mod C)$   
이때 h는 cycle node의 위치라고 생각할 수 있다.
P2는 실질적으로 P1에 비해서 2배 빠르게 움직이고 있고, P1은 F를 가리키고 있으니 현재 P2는 $2F$만큼 움직인 상황이다.   
P1은 현재 cycle node 0을 가리키고 있다.

이때 $C-h$만큼의 iteration을 더 수행하게 되면, P1은 C-h를 가리키게 된다. 그리고 P2도...   
$h + 2(C-h) = 2C-h \equiv C-h (mod C)$   
$C-h$ 위치에 다다르게 된다. 그래서 P1 과 P2가 만나게 되어 Cycle이 있다는 것을 알 수 있게 된다.   
(만약 cycle이 아닌 경우에는 P2가 None(Linked-list의 끝)을 만나게 될 것이다.)

## What about the finding the entrance node of cycle? 
위에서는 linked-list에 cycle node 존재 여부를 확인하였다. 그렇다면 cycle entrance는 어떻게 찾을 것인가?

P1은 다시 linked-list의 head node를 가리키고 P2는 앞서 찾은 P1와 P2이 처음으로 만난 C-h를 가리키게 한다.   
여기서 다시 생각하보면, 앞서 $F \equiv h (mod C) ==> F = nC + h$ 로 정의가 되었으며, P2의 시작 포인트인 $C-h$로 부터 F 만큼 이동해보자.   
$(C-h) + (nC+h) = (1+n)C$ 가 되므로 cycle node의 배수의 위치가 되므로, cycle entrance 인 node 0에 위치하게 된다.   
이때 P1도 Noncycle node의 길이만큼인 F만큼 이동하게 되므로, 최종적으로 Cycle entrance에서 P1과 P2가 만나게 된다.   
이때의 지점을 cycle entrance로 찾게 된다.


(그림 추가 예정)

약간의 정수론적인 상식을 더하여 생각해보면 재미있는 알고리즘 인 것 같으나 이런 생각을 하고 사는 사람들은 어떤 세상을 사는건지 궁금하다.


