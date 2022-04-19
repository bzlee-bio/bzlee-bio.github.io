---
title:  "Algorithm study - Trie"
toc: true
toc_sticky: true
use_math: true
toc_label: "Contents"
categories: 
  - Algorithm
tags:
  - Algorithm
  - Trie
---

모두연에서 진행하는 풀잎스쿨 중 코딩습관 기르기를 신청하게 되었다. 앞으로 알고리즘 문제를 풀다가 잘 모르는 개념을 정리해보려고 한다...   
이번주에는 Trie라는 새로운 개념을 만나게 되어 정리하게 되었다.   

# Trie
Trie는 k-ary search tree의 한 종류인 tree 데이터 구조를 말한다.   

## 무지해서 필요한 background
일단 *search tree*에 대해서 간단히 알아보면, 
tree를 구성할 때 각각의 node의 key보다 작은 key를 가지는 node를 subtree의 왼쪽에, 반대로 더 큰 key를 가지는 경우 subtree의 오른쪽에 구성한다고 한다. 아마 search하는데 효율을 위해 이처럼 구성하는 것 같다. (관련 내용은 추후에 다시 정리를...)   
그리고 *k-ary tree*는 각각의 node가 최대 k개의 children을 가지는 것을 의미한다. 즉, binary tree는 k-ary tree 중 $k=2$인 special case라고 정의할 수 있다고 한다. 

즉, *k-ary search tree*인 trie는 위에 제시된 두개의 큰 특징을 가지고 있는 tree인 것 같다..   
*Trie*는 string 타입의 데이터를 이용하여 구성되는 경우가 많으며, 각 node가 string을 값으로 가지고 있는 것이 아니라 character를 가지고 있다.   
아직 정확한 용어와 개념이 있질 않아서 더 정리를 할 수 가 없다...   


![Ref; Wikipedia](/imgs/041822_post/Trie_example.png){: .align-center}
위 이미지가 Trie의 예시이다. (참고: https://en.wikipedia.org/wiki/Trie)

## Operations
Tries를 이용하여 string key를 이용한 insertion, deletion, lookup을 수행할 수 있다.   

(정리중)