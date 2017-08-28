---
layout: post
section-type: post
title: Jest를 이용해 Snapshot testing하기
category: react
tags: [ 'react', 'jest' ]
---

> React Application을 testing하기 위한 Jest를 이용해서 snapshot testing을 해보자. [공식 문서](https://facebook.github.io/jest/docs/snapshot-testing.html#content) 참고.

<br>

## Snapshot testing

Snapshot test는 UI가 예측못하게 바뀌는 걸 방지하도록 할 때 사용되는 유용한 툴 입니다.  
일반적인 모바일 앱에서의 snapshot 테스트 케이스는 UI component를 렌더링하고, 스크린샷을 찍고, 테스트 부근에 저장된 레퍼런스 이미지와 찍은 것들을 비교합니다.

테스트는 만약 두개의 이미지가 매칭되지 않으면 실패합니다. 즉, 이는 예상치 못한 변화 혹은 스크린샷이 UI component의 새로운 버전으로 업데이트 되어야 할 때를 말합니다.

<br>

## Jest에서 Snapshot testing

리액트 컴포넌트들을 테스팅 할 때 비슷한 접근 방법을 사용할 수 있습니다.  
전체 앱을 빌드해야 하는 그래픽적인 UI를 렌더링 하는 대신에, React tree에 직렬화 가능한 값을 빠르게 생성하는 테스트 렌더러를 사용할 수 있습니다.  
간단한 [링크 컴포넌트](https://github.com/facebook/jest/blob/master/examples/snapshot/Link.react.js)의 [테스트 예시](https://github.com/facebook/jest/blob/master/examples/snapshot/__tests__/Link.react-test.js)를 살펴봅시다.

``` js
import React from 'react';
import Link from '../Link.react';
import renderer from 'react-test-renderer';

it('renders correctly', () => {
  const tree = renderer.create(
    <Link page="http://www.facebook.com">Facebook</Link>
  ).toJSON();
  expect(tree).toMatchSnapshot();
});
```

테스트가 가장 먼저 실행되었을 때, Jest는 snapshot 파일을 다음과 같이 생성합니다.

``` js
exports[`Link renders correctly 1`] = `
<a
  className="normal"
  href="http://www.facebook.com"
  onMouseEnter={[Function]}
  onMouseLeave={[Function]}>
  Facebook
</a>
`;
```

스냅샷 생성물은 코드 변화와 함께 커밋되어야 합니다. Jest는 코드 리뷰하는 동안 읽기 쉬운 snapshot 파일들을 만들기 위해 [pretty-format](https://github.com/facebook/jest/tree/master/packages/pretty-format) 이란 것을 사용합니다.  
바로 이후에 일어나는 테스트는 Jest가 이전 스냅샷에 렌더된 출력물들을 간단하게 비교하는 것을 실행합니다. 이들이 매치가 된다면, 테스트는 통과되고, 그렇지 않으면, 테스트 러너는 고쳐야 할 코드에서 버그를 발견하거나 구현물이 변화되고 스냅샷은 업데이트 되어야 할 것 입니다.  

스냅샷 테스팅이 어떻게 작동하고 왜 만들었는지에 대해서 더 많은 정보는 [이 링크](https://facebook.github.io/jest/blog/2016/07/27/jest-14.html)에 잘 나와있다.  
스냅샷 테스팅을 사용해야한다면 좋은 센스를 가지기 위해 이 [블로그 게시글](http://benmccormick.org/2016/09/19/testing-with-jest-snapshots-first-impressions/)을 읽는 것을 추천한다. 스냅샷 테스팅 관련 [egghead 비디오](https://egghead.io/lessons/javascript-use-jest-s-snapshot-testing-feature?pl=testing-javascript-with-jest-a36c4074)도 있다.

<br>

### 스냅샷 업데이트

버그가 도입된 후 스냅샷 테스트가 실패하면 쉽게 발견할 수 있습니다.
이런 상황이 발생하면, 그 issue에 가서 고치고 다시 snapshot test를 통과할 수 있도록 하면 됩니다.
자, 의도적인 구현 변화를 통해 스냅샷 테스팅을 실패하는 케이스를 살펴봅시다.

예제에서 링크 컴포넌트가 가리키고 있는 주소를 바꾸는 일을 고의로 발생시키는 상황을 봅시다

```js
// Updated test case with a Link to a different address
it('renders correctly', () => {
  const tree = renderer.create(
    <Link page="http://www.instagram.com">Instagram</Link>
  ).toJSON();
  expect(tree).toMatchSnapshot();
});
```

이 케이스에선, 다음과 같은 출력이 나옵니다.

![snapshot_test](/images/posts/snapshot_test.png)

이전에 우리는 단지 다른 주소를 가르키게 하는 컴포넌트를 업데이트 했습니다. 이는 이 컴포넌트에서 스냅샷의 변화를 예측되기 때문에 이는 올바른 결과입니다. 스냅샷 테스트 케이스는 업데이트된 컴포넌트의 스냅샷이 더이상 이 테스트 케이스의 스냅샷 구조와 매칭되지 않기 때문에 실패하는 것입니다.

이를 해결하기 위해, 스냅샷 구조를 업데이트 할 필요가 있습니다. 다시 스냅샷을 생성하기 위해서는 그냥 flag를 jest에 추가 하는 것만으로도 가능합니다.

``` shell
$ jest --updateSnapshot
```

위 커맨드를 실행해서 변경 내용을 적용하면 됩니다. 만약 원한다면 -u flag를 사용해서 스냅샷을 재생성 할 수 있습니다. 이것은 모든 실패한 스냅샷 테스트 들에 대해서 스냅샷 구조물(artifacts)들을 재생성합니다. 의도치 않은 버그 때문에 스냅샷 테스트들이 추가적으로 실패하고 있다면, 우리는 재생성 스냅샷 을 하기 전에 버그를 고칠 필요가 있습니다.

만약 재 생성된 스냅샷 테스트 케이스들을 제한 하고 싶다면, --testNamePattern flag를 이용해 패턴들을 매칭하는 이러한 테스트들을 통해 스냅샷들을 다시 기록 할 수 있습니다.

[스냅샷 예제](https://github.com/facebook/jest/tree/master/examples/snapshot)들을 clone해서 기능들을 실행해 볼 수 있습니다. Link 컴포넌트를 수정하고, Jest를 실행하는 예제입니다.

<br>

### 테스트들은 결정론적(Deterministic)이어야 한다.

테스트들은 결정론적(Deterministic)이어야 합니다. 이는 바뀌지 않았던 컴포넌트들에서 여러 시간동안 같은 테스트들을 실행해도 매번 같은 결과가 나와야 한다는 것을 의미합니다.  
생성된 스냅샷들은 특정한 플랫폼에 종속되지 말아야 하며 다른 결정론적이지 않은 데이터들을 포함하지 말아야 할 의무가 있습니다.

예를 들어, Date.now()를 사용하는 Clock 컴포넌트가 있다고 하면,  
컴포넌트로 부터 생성된 스냅샷은 테스트 케이스가 실행될 때마다 매번 달라질 것입니다.  
이러한 경우 테스트가 실행 될 때마다 유지되는 값들을 반환 하기 위해서 Date.now() 함수를 [mock](https://facebook.github.io/jest/docs/mock-functions.html)할 수 있습니다.

``` js
Date.now = jest.fn(() => 1482363367071);
```

이제, 스냅샷 테스트 케이스가 사용될 때마다, Date.now()는 1482363367071을 유지할 것입니다.  
이렇게 하면 테스트 실행 시기와 상관없이 구성요소에 대해 동일한 스냅 샷이 생성됩니다.

<br>

## 리액트, 리액트 네이티브와 스냅샷 테스팅

배웠던 것처럼, 스냅샷 테스팅은 React와 React Native 테스트 작성을 더 쉽게 만들어 줍니다.  
[React 튜토리얼](https://facebook.github.io/jest/docs/tutorial-react.html)과 [React Native 튜토리얼](https://facebook.github.io/jest/docs/tutorial-react-native.html)을 통해 스냅샷 테스팅을 확인하세요.

<br>

## 추가적인 사용법

스냅샷은 직렬화가능한(serializable) 값을 캡쳐할 수 있습니다.  
공통적인 예제들은 [CLI 출력 스냅샷하기](https://github.com/facebook/jest/blob/master/integration_tests/__tests__/console-test.js) 또는 API response를 참고하세요.  
