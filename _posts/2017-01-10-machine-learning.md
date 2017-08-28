---
layout: post
section-type: post
title: KMOOC 머신러닝 정리 - WEEK3
category: machine-learning
tags: [ '머신러닝', 'machine-learning', 'kmooc' ]
---

# WEEK 3

### optimal classification concept
* y가 real Y가 같지 않을 확률을 최소화 하는 f를 찾아낸 것을 f*라 하자. 이것을 optimize라 한다.
* 확률을 판단하는데 있어서, 곡선 형태의 확률 그래프와 직선 형태의 확률 그래프를 만들 수 있는데, 곡선 형태의 확률의 error 차이를 f*(Bayes Risk) 라고 한다.
* Interaction에 관한 부분을 무시하고 계산하는 것을 naive Bayes classifier라고 한다.

### naive Bayes classifier

* Dataset for optimal classifier learning
 * f*=P(X=x|Y=y)P(Y=y)인데, P(Y=y)는 구하기 쉽지만 P(X=x|Y=y)는 구하기가 까다롭다. x의 수가 늘어나면, 더 어려워 진다.
* Why need an additional assumption?
 * model은 unrealistic한 가정들을 관대하게 해왔지만, 이제 그 모델은 배우는 것이 불가능하게 되었다. 그래서 다른 가정을 더할 때이다.
 * x의 수를 줄이지 않고, dataset을 컨트롤 할 수 있는 방법이 무엇이 있을까? -> Conditional Independence라는 가정을 도입
* Conditional Independence
 * x가 서로 '독립'이라는 가정 하에 계산을 해보는 것이다.
 * 가정이 성립 한다면, ```P(x1,x2|y) = P(x1|y)P(x2|y) ``` 또한 성립하게 된다.
 * latent한 변수가 주어진다면, 이것은 conditionally indepenent 라고 할 수 있다.

### Apply the naive Bayes classifier to a case study of a textmining

<!-- *  -->
