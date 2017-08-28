---
layout: post
section-type: post
title: React server side rendering과 code splitting을 손쉽게 해보자! - Next.js 시작하기
category: nextjs
tags: [ 'nextjs' ]
---

> react는 서버사이드 렌더링을 염두에 두고 만들어졌지만, 서버사이드 렌더링을 붙이기엔 러닝커브도 꽤 높고 설정해야 할 것들이 많다.    
> 그래서 리액트를 손쉽게 서버사이드 렌더링으로 구현할 수 있고, 여러 장점들이 있는 next.js에 대해서 알아보자.

## 서버사이드 렌더링(Server side rendering)이란?

 클라이언트 측 렌더링은 빈 HTML 문서를 서버에서 반환한 후, **필요한 자바스크립트를 모두 다운로드 한 다음** 에 렌더링이 수행됩니다.  
이에 비해 서버 사이드 렌더링은 서버는 **화면이 이미 렌더링 된 HTML문서** 를 웹브라우저로 전송하기 때문에, 사용자는 화면에 있는 콘텐츠들을 금방 볼 수 있습니다.  
하지만 화면에 있는 콘텐츠를 볼 수는 있지만 애플리케이션과 인터렉션은 불가능하고 필요한 javascript가 전부 다운로드 된 후 가능해집니다.
 이렇게 서버에 부담을 더 주기 때문에

더 궁금하다면 [왜 React와 서버사이드 렌더링인가?](https://subicura.com/2016/06/20/server-side-rendering-with-react.html) 포스트를 참고해보자.

## 코드 스플릿팅(Code splitting)이란?
