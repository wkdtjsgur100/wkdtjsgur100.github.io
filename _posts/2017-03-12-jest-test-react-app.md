---
layout: post
section-type: post
title: Jest를 사용해 React App을 테스트
category: react
tags: [ 'jest', 'react' ]
---

## Setup

먼저 [create-react-app](https://github.com/facebookincubator/create-react-app)을 사용해서 리액트 앱을 설치한다.

``` shell
$ brew install node # node 설치
$ npm install -g create-react-app # 리액트 앱 설치
$ create-react-app my-app # my-app이라는 이름의 리액트 앱 설치
$ cd my-app/
$ npm start # 서버 실행
```

> create-react-app은 테스트를 하는 방법에 대해서 [github running tests](https://github.com/facebookincubator/create-react-app/blob/master/packages/react-scripts/template/README.md#running-tests)라는 곳에 자세히 설명해놓았고 참고해서 간결하게 작성했다.

## 테스트 작동

### 파일 이름 규칙

1. ```__test__``` 폴더 내 이름이 .js로 끝나는 파일들
2. 이름이 .test.js로 끝나는 파일
3. 이름이 .spec.js로 끝나는 파일

> 참고: create-react-app은 첫 번째 방법을 추천한다고 한다.

CI(CircleCI), coverage와 연동하기 위해,

``` shell
CI=true npm test -- --coverage
```

을 사용해서 테스트를 돌렸다.

> ```CI=true``` 옵션을 사용하면, test가 background에서 실행되는것이 아니라 한번만 실행된다.

그리고 node_module을 cache 디렉터리에 넣기 위해 circle.yml 파일에

``` yml
dependencies:
   cache_directories:
     - "linku_frontend/linku/node_module"
```

을 추가했다.

### 테스트 작성

테스트를 만들기 위해서, it()(또는 test()) 블록을 test 이름과 추가한다. describe() 블록에 있는 것들을 논리적으로 그룹화하기 위해 it()으로 감쌀 수도 있지만 추천하지도 않고 반드시 필요하지도 않다.  
Jest는 assertion들을 만들기 위해서 expect()라는 내장된 함수를 제공한다.  
기본적인 테스트를 보면 다음과 같다.

``` python
import sum from './sum';

it('sums numbers', () => {
  expect(sum(1, 2)).toEqual(3);
  expect(sum(2, 2)).toEqual(4);
});
```

그리고 jest.fn()함수와 expect(fn).toBeCalled()란 함수를 사용할 수도 있는데 이는 mock함수나 spies 를 생성할 떄 사용된다.

### 컴포넌트 테스트

컴포넌트가 완전히 렌더링 되는지, 얕은 수준의 rendering인지 화인하고, 컴포넌트 렌더, 상태 변화 등을 테스팅하는 여러 수준의 테스팅 기술들이 있다.  

일단 컴포넌트에 대한 간단하고 실체가 없는(smoke) test들을 만드는 것으로 시작해보자.

``` js
import React from 'react';
import ReactDOM from 'react-dom';
import App from './App';

it('renders without crashing', () => {
  const div = document.createElement('div');
  ReactDOM.render(<App />, div);
});
```

이 테스트는 컴포넌트가 마운트되었고 렌더링 하는 동안 제대로 할당되었는지 테스트하는 것이다. 이러한 테스트는 작은 노력으로 많은 가치를 낸다.  

컴포넌트를 바꾸는 것에의해서 버그가 발견된다면, 어플리케이션 테스팅의 일부를 통해서 더 깊은 인사이트를 얻을 수 있게 된다.  

만약 자식 컴포넌트로 부터 분리된 곳에서 컴포넌트를 테스트 하고 싶다면, [Enzyme](http://airbnb.io/enzyme/)의 [shallow() rendering API](http://airbnb.io/enzyme/docs/api/shallow.html)를 사용하면 된다.  
이를 사용하는 예제를 보자.

``` shell
npm install --save-dev enzyme react-addons-test-utils
```

``` js
import React from 'react';
import { shallow } from 'enzyme';
import App from './App';

it('renders without crashing', () => {
  shallow(<App />);
});
```

이 테스트는 <App>을 렌더할 뿐이고 더이상 깊은 테스트를 하지 않는다. shallow 렌더링은 독립된 단위테스트를 위해서는 좋지만, 컴포넌트가 올바르게 통합하는 지 확인하고 싶을 때도 있다. 그땐 Enzyme가 [mount()를 사용한 full rendering](http://airbnb.io/enzyme/docs/api/mount.html)을 지원한다. 테스팅 상태 변화와 컴포넌트 생명주기를 위해서 mount() 같은 것을 사용할 수 있다.

좀더 여러 테스팅 기술을 위해서 Enzyme documentation을 살펴보자. Enzyme는 assertion을 위해 Chai와 Sinon을 사용하지만 Jest 에서 expect()와 jest.fn()을 내장하고 있기 때문에 꼭 사용할 필요는 없다.  

아래는 Jest matcher를 사용해서 특정 출력을 aasert하는 Enzyme의 예제이다.

``` python
import React from 'react';
import { shallow } from 'enzyme';
import App from './App';

it('renders welcome message', () => {
  const wrapper = shallow(<App />);
  const welcome = <h2>Welcome to React</h2>;
  // expect(wrapper.contains(welcome)).to.equal(true);
  expect(wrapper.contains(welcome)).toEqual(true);
});
```

Jest matcher의 문서는 [이 링크](http://facebook.github.io/jest/docs/expect.html)에 자세히 나와있으며, 추가적으로, [jest-enzyme](https://github.com/blainekasten/enzyme-matchers)는 읽을 수 있는 matcher들의 테스트를 간단하게 만들 수 있다.

아래는 jest-enzyme의 예제이다.

```
expect(wrapper).toContainReact(welcome)
```

사용하기 위해선, 다음과 같은 환경설정이 필요하다.

```
npm install --save-dev jest-enzyme
```

``` python
import 'jest-enzyme';
```
