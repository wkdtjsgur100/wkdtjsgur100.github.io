---
layout: post
section-type: post
title: redux-saga declarative effects
category: react
tags: [ 'react', 'redux' ]
---

redux-saga에서, saga들은 generator function들을 사용해서 실행됩니다. saga 로직을 표현하기 위해 generator 부터 명백한 javascript 객체들을 산출(yield) 합니다.  
이러한 객체들을 Effects라고 부르는데, 이 Effect는 미들웨어에 의해서 해석되는 몇몇 정보들을 포함하는 간단한 객체입니다. 몇몇 기능들(비동기 함수, 스토어에 액션을 전송하는 것들)을 수행하는 미들웨어의 명령어들같은 Effect들을 볼 수 있습니다.  

Effect들을 생성하기 위해, redux-saga/effects 패키지에 있는 라이브러리에 의해 제공되는 기능들을 사용합니다.  

이 단원에서 그리고 계속해서 우리는 몇몇 기초적인 effect들을 소개 할 것입니다. 그리고 어떻게 쉽게 테스트 될수 있는 saga들을 허용하는 지에 대해 살펴봅니다.

Saga는 여러개의 형식들에서 effect들을 산출(yield)할 수 있습니다. 가장 간단한 방법은 Promise를 산출(yield)하는 것입니다.  

예를 들어 PRODUCTS_REQUESTED 액션을 관찰하는 saga를 가지고 있다고 가정합시다. 각각 매칭되는 액션에서, 서버로 부터 상품들의 리스트를 가져오는 작업을 수행합니다.  

``` js
import { takeEvery } from 'redux-saga/effects'
import Api from './path/to/api'

function* watchFetchProducts() {
  yield takeEvery('PRODUCTS_REQUESTED', fetchProducts)
}

function* fetchProducts() {
  const products = yield Api.fetch('/products')
  console.log(products)
}
```

위의 예시에서, 내부의 generator(generator 기능들에서, yield의 오른쪽에 어떤 표현식들은 caller들)로 부터 직접적으로 Api.fetch를 발생시키고 있습니다. 
