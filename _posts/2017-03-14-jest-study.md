---
layout: post
section-type: post
title: jest, enzyme를 이용한 react application 테스팅 [ 작성중 ]
category: react
tags: [ 'react', 'jest' ]
---

> 이번에 Jest를 공부하게 되면서 react를 테스팅 하기 위해선 어떤 방법으로 해야할 지 생각하게 된 걸 간단히 정리했다.

## React Component Test

React는 Component 단위로 어플리케이션이 제작되기 때문에, 대부분 Component 단위로 테스팅이 이루어진다.  
Component를 테스팅을 할 수 있는 여러가지 방법이 있는데 그 방법들을 보고 예제들과 함께 정리해 봤다.

## Jest를 이용한 테스팅 방법

[Jest](https://facebook.github.io/jest/docs/getting-started.html#content)를 이용해서 할 수 있는 가장 간단한 테스팅 코드는 아래와 같다.  

``` js
import sum from './sum'

test('adds 1 + 2 to equal 3', () => {
  expect(sum(1, 2)).toBe(3);
});
```

test() 함수는 첫 번째 인자로 그 테스팅을 설명할 description을 인자로 받는다. 두 번째 인자로는 테스팅 코드가 포함된 함수를 매개변수로 받는다.  
그리고 expect()라는 함수는 인자로 받는 함수의 결과값과, toBe() 함수의 인자 값을 비교해 참이면 테스트 성공이고 거짓이면 테스트 실패를 발생시킨다.  
관련된 함수는 [jest 공식 문서](https://facebook.github.io/jest/docs/expect.html#content)를 확인하자.  

여기서 test() 함수가 아니라 it() 함수를 쓸 수도 있다.  
describe() 함수는 it()과 test()를 묶을 수 있는 더 상위 개념의 함수이다.  
이 방법은 Airbnb에서 자주 쓰는 방식인데, jest 공식 문서에서는 describe 함수로 묶는 것을 권장하지 않는다고 했지만,  
더 큰 단위로(뒤에서 컴포넌트 단위로 작은 단위의 테스트를 묶을 때 유용하다.) 테스트를 묶는다는 것이 유용하다. 아래는 예제이다.

``` js
import sum from './sum';
import mul from './mul';

describe('operator tests', () => {
    it('sum test', () => {
      expect(sum(1,3)).toBe(4);
    });
    it('multiple test', () => {
      expect(mul(1,3)).toBe(3);
    });
});

```

describe 함수도 it()이나 test() 함수와 마찬가지로 첫 번째 인자로 테스팅을 설명할 description을 인자로 받는다. 두 번째 인자로는 it()함수나 test() 함수가 포함된 함수를 매개변수로 가진다.  

## Shallow Rendering을 이용한 컴포넌트 테스트

## Full DOM Rendering을 이용한 컴포넌트 테스트

## Static Rendering을 이용한 컴포넌트 테스트

## Jest Snapshot Test

출처 --  
- http://airbnb.io/enzyme/docs/api/shallow.html  
- https://facebook.github.io/jest/
