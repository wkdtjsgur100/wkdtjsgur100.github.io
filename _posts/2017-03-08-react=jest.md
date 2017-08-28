---
layout: post
section-type: post
title: React Application을 Testing하기 위한 Jest 시작하기
category: react
tags: [ 'jest', 'react' ]
---

> 우리는 프론트엔드를 react.js로 개발하려고 한다. 그런데 TDD를 이용해서 프로그램을 개발 하려면 react를 테스팅하기 위한 facebook의 공식 테스팅 툴 Jest를 사용해야 했고, [공식 문서](https://facebook.github.io/jest/docs/getting-started.html#content)를 참고해 글을 작성했다.

!! react 프로젝트는 생성이 되어있다고 가정한다.
본인의 react 프로젝트는 [inflearn 사이트](https://www.inflearn.com/course-status-2/)를 참고해서 만들었고,  
이 프로젝트는 webpack과 es2015를 사용한다.( 이 프로젝트를 기준으로 작성 )

<br>

## Jest의 장점

1. 쉬운 설치
 - 완전하면서 쉬운 javascript testing 솔루션이다. 어떤 리액트 프로젝트에서도 즉시 사용할 수 있다.
2. 즉각적인 피드백
 - 빠르고 인터렉티브한 watch mode는 바뀐 테스트 파일들에게만 작동하고 신호를 빠르게 주는데 최적화 되어있습니다.
3. 스냅샷 테스팅
 - React Tree 또는 어떤 값들의 스냅샷을 캡쳐해서 UI testing을 간소화 시키고, 시간이 지남에 따른 상태 변화를 분석합니다.

그 외의 장점들은 [공식 문서](https://facebook.github.io/jest/)의 아랫부분을 보면 확인 가능하다.

<br>

## 시작하기

__npm을 이용한 Jest 설치__

``` shell
$ npm install --save-dev jest # react 프로젝트 폴더 내에서 실행한다.
```

두개의 숫자를 더하는 함수를 만드는 테스트를 작성해보자.  
먼저 sum.js 파일을 만들자.

``` javascript
function sum(a, b) {
  return a + b;
}
module.exports = sum;
```

그리고 나서, sum.test.js 파일을 만들어서 테스팅을 한번 해보자.

``` javascript
const sum = require('./sum');

test('adds 1 + 2 to equal 3', () => {
  expect(sum(1, 2)).toBe(3);
});
```

package.json의 script부분에 다음을 추가하자.

``` json
"scripts": {
  "test": "jest"
}
```

마지막으로 npm test를 실행하면 jest는 다음과 같은 메세지를 표시한다.

``` shell
PASS  ./sum.test.js
✓ adds 1 + 2 to equal 3 (5ms)
```

이 테스트는 두가지를 비교하는 expect와 toBe같은 Matcher들을 사용했다.  
Jest로 테스트 할 수 있는 다른 것들을 살펴보려면, [Matcher 사용법](https://facebook.github.io/jest/docs/using-matchers.html)을 참고하자.

<br>

## 커맨드 라인에서 실행하기

CLI에서 jest를 실행할 때 다양한 옵션들과 함께 사용할 수 있다.  
여기 config.json이라는 configuration 파일을 사용해서 my-test와 매칭되는 파일들에 Jest를 돌리고,  
실행 후에 native OS notification에 보여주는 방법에 대해 아래에 나와있다. 좀 더 다양한 옵션은 [CLI Options](https://facebook.github.io/jest/docs/cli.html)를 참고하자.

``` shell
$ jest my-test --notify --config=config.json
```

<br>

## 추가적인 설정

### babel을 사용해 test파일에 es2015 문법을 적용시켜 보자.

babel-jest와 regenerator-runtime 패키지들을 설치하자.

``` shell
$ npm install --save-dev babel-jest regenerator-runtime
```

그리고 프로젝트 루트 폴더에 .babelrc 파일을 추가하자.  
예를 들어, __ES6와 react에서 babel-preset-es2015와 babel-preset-react를 이용하고 싶다면__ 다음과 같이 한다.

``` shell
$ touch .babelrc # .babelrc 파일 생성
```

.babelrc

``` json
{
  "presets": ["es2015", "react"]
}
```

이제 위의 sum.test.js 파일을 ES2015 문법을 적용해서 바꿔보자

``` javascript
import sum from './sum';

test('adds 1 + 2 to equal 3', () => {
  expect(sum(1, 2)).toBe(3);
});
```

잘 작동되는 것을 알 수 있다.
