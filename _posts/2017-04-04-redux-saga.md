---
layout: post
section-type: post
title: redux-saga에서 제공하는 helper effects
category: redux
tags: [ 'react', 'redux', 'redux-saga' ]
---

> redux-saga에서 saga helper에 관한 [공식 블로그](https://redux-saga.github.io/redux-saga/docs/basics/UsingSagaHelpers.html)에서 제공하는 문서를 번역, 요약한 자료입니다.

## redux-saga 란?

> redux-saga is a library that aims to make side effects (i.e. asynchronous things like data fetching and impure things like accessing the browser cache) in React/Redux applications easier and better.

> 번역 : redux-saga는 사이드 이펙트(예를 들어, 데이터 불러오기 같은 비동기적인 것들 그리고 브라우저 캐시에 접근하는 것과 같이 순수하지 않은 것들)를 만들어내는데 react/redux 애플리케이션에서 더 쉽고 더 편하게 해주는데 중점을 둔 라이브러리입니다.

> 출처 - [redux-saga github](https://github.com/redux-saga/redux-saga)


## saga helper를 사용해보자.

redux-saga는 몇몇 특정 액션들이 스토어에 보내질 때, 여러 task들을 함께 실행하기 위한 내부적인 함수를 감싸는 몇몇 helper effect들을 제공합니다.  

The helper function들은 더 낮은 레벨 API의 가장 상위에 만들어집니다. 심화 section에서, 이러한 함ㅅ들이 어떻게 구현되는지 볼 수 있을 것입니다.

가장 먼저 takeEvery 란 함수는, redux-thunk과 비슷한 기능을 제공하고 가장 흔히 사용되는 함수입니다.

공통의 AJAX 예제를 이용해서 설명해보겠습니다. 이 예제는 Fetch 버튼을 클릭할 때마다  FETCH_REQUESTED 액션이 전달 되는 것입니다. 우리는 이 액션을 서버로 부터 몇가지 데이터들을 가져오는 task를 실행하는 것으로 이 액션을 다루겠습니다.  

먼저 우리는 비동기 액션을 실행하는 task를 만듭니다.
``` js
import { call, put } from 'redux-saga/effects'

export function* fetchData(action) {
   try {
      const data = yield call(Api.fetchUser, action.payload.url)
      yield put({type: "FETCH_SUCCEEDED", data})
   } catch (error) {
      yield put({type: "FETCH_FAILED", error})
   }
}
```

그리고 FETCH_REQUEST 액션을 수행할 때마다 위의 task를 수행하려면:

``` js
import { takeEvery } from 'redux-saga/effects'

function* watchFetchData() {
  yield takeEvery('FETCH_REQUESTED', fetchData)
}
```

위의 예제에서, takeEvery는 동시에 시작되는 여러 개의 fetchData instance들을 허용합니다. 이 순간에 이전의 하나 혹은 더 많은 아직 제거되지 않은 fetchData 작업들이 아직 있는 동안 새로운 fetchData 작업을 시작합니다.

만약에 단지 가장 마지막에 발생된 request의 response를 얻고 싶다면(예를 들어 항상 데이터의 가장 마지막 버전을 보여주는 것), takeLatest helper를 사용하면 됩니다.  

``` js
import { takeLatest } from 'redux-saga/effects'

function* watchFetchData() {
  yield takeLatest('FETCH_REQUESTED', fetchData)
}
```

takeEvery와는 다르게, takeLatest는 어떤 순간에 실행되는 단 하나의 fetchData 작업만 허용합니다. 그리고 이것은 가장 마지막에 시작되었던 작업일 것입니다. 만약 다른 fetchData 작업이 시작되었을 때 이전의 작업이 여전히 실행 중이라면, 이전의 작업은 자동적으로 취소됩니다.  

서로 다른 액션들을 보고 있는 여러개의 saga들을 가지고 있다면, 이것들을 함께 실행하는데 사용되는 fork 같은 일을하는 built-in helper들로 여러개의 watcher를 만들 수 있습니다.(나중에 fork에 대해 얘기할 것입니다. 지금은 백그라운드에서 여러개의 saga들을 시작하는 것을 허용하는 이펙트 가 있다고 생각하세요.)

예를 들면 :

``` js
import { takeEvery } from 'redux-saga'

// FETCH_USERS
function* fetchUsers(action) { ... }

// CREATE_USER
function* createUser(action) { ... }

// use them in parallel
export default function* rootSaga() {
  yield takeEvery('FETCH_USERS', fetchUsers)
  yield takeEvery('CREATE_USER', createUser)
}
```
