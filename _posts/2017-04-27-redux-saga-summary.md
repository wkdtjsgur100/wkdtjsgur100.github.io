---
layout: post
section-type: post
title: Redux saga에 대해서[작성 중]
category: redux
tags: [ 'redux-saga', 'redux' ]
---

> React를 공부할 때, redux 구조는 자연스럽게 접하게 되고 비동기 통신을 하게 되면 redux-thunk와 이를 개선한 redux-saga를 접하게 된다.
> redux-saga는 생각보다 엄청 어려운 개념은 아닌데, 자료들이 대개 너무 어렵게 설명되어 있거나 영어로 된 문서인 경우가 많아 내가 이해했던 부분을 최대한 쉽게 정리해서 적어보았다.

> 본 문서를 읽기에 앞서서, javascript 비동기 동작에 대한 이해가 부족한 분은 [ES6 비동기 프로그래밍 관련 글](https://wkdtjsgur100.github.io/blog/2017/03/30/es6-async-programming/)을 읽고 오시길 추천합니다.

## redux-saga란?

redux-saga 공식 사이트에서는 다음과 같이 설명되어 있는데,
> redux-saga 는 리액트/리덕스 애플리케이션의 사이드 이펙트, 예를 들면 데이터 fetching이나 브라우저 캐시에 접근하는 순수하지 않은 비동기 동작들을, 더 쉽고 좋게 만드는 것을 목적으로하는 라이브러리입니다.

너무 어렵고 애매하게 적혀있어서, 좀 더 쉽고 명확하게 정의를 해보자면
__redux-saga는 redux 구조에서 '비동기 동작'을 수행할 때 운영체제의 프로세스 관리와 같이 비동기 동작을 관리하기 위한 미들웨어 입니다.__

![ㅇㅇ](https://github.com/reactkr/learn-react-in-korean/blob/master/translated/assets/deal-with-async-process-by-redux-saga/redux-saga-diagram.png)


-- 참고 링크  
https://github.com/reactkr/learn-react-in-korean/blob/master/translated/deal-with-async-process-by-redux-saga.md  
https://redux-saga.js.org/docs/introduction/index.html  
