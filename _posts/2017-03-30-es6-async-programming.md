---
layout: post
section-type: post
title: ES6 비동기 프로그래밍에 대하여
category: javascript
tags: [ 'javascript', 'es6' ]
---

## 자바스크립트 호출 스택

자바스크립트의 함수 호출은, 호출 스택인 스택으로 인해 관리됩니다.

## browser event loop

각 브라우저 탭은 싱글 프로세스(single process : the event loop)에서 실행됩니다.  
병렬로 함께 실행되는 다른 프로세스(timers, input handling, etc)는 각각의 task를 task queue에 추가하여 프로세스와 통신합니다.  

### 타이머

setTimeout(callback, ms);

이와 같은 소스가 있을 경우, ms(millisecond) 후에 callback이 __task queue에 추가__ 됩니다. 만약 event loop가 차단 당하면 지정된 시간 이후 보다 나중에 setTimeout callback이 실행되게 됩니다.

### Run to completion

항상 현재 task는 다음 task가 실행되기 전에 끝납니다.

``` js
setTimeout(function () { // (A)
    console.log('Second');
}, 0);
console.log('First'); // (B)
```
위와 같은 코드가 있을경우, 출력결과는 항상 다음과 같습니다.

``` text
First
Second
```

### Blocking the event loop

위에서 설명한 것에 따라 해당 프로세스 안에서 긴 연산작업을 수행하게 하여 사용자 인터페이스를 멈추게 할 수도 있습니다.

### 블로킹 피하기

위의 블로킹을 피하는 방법에 앞서 메인 프로세스 안에서 엄청 긴 연산작업을 수행하면 안되고 필요하다면 다른 프로세스로 옮겨야 합니다. event loop에서 수행시키고, 완료되면 통보하게 합니다. 예를 들어, setTimeout을 이용해 비 동기적인 sleep을 사용하는 방법이 있습빈다.

### 비동기적 결과 수신

비동기적 결과를 수신하기 위해 두가지 패턴이 있습니다
1. events

예를 들어 , XMLHttpRequest API가 있는데 아래는 이에 대한 예시입니다.

``` js
var req = new XMLHttpRequest();
req.open('GET', url);

req.onload = function () {
    if (req.status == 200) {
        processData(req.response);
    } else {
        console.log('ERROR', req.statusText);
    }
};

req.onerror = function () {
    console.log('Network Error');
};

req.send(); // Add request to task queue

```

2. 암시적 요청(Implicit requests)

예를 들면 IndexedDB API 가 있습니다. 아래는 예시 입니다.  

``` js
var openRequest = indexedDB.open('test', 1);

openRequest.onsuccess = function (event) {
    console.log('Success!');
    var db = event.target.result;
};

openRequest.onerror = function (error) {
    console.log(error);
};
```

이것은 요청 객체를 만들고, 이벤트 리스너를 등록합니다. 그러나 open()을 사용하여 요청을 명시적으로 queue에 추가할 필요 없이
task가 완료된 후에 실행됩니다. 이는 run-to-completion 특징 때문에 항상 안전하다는 사실을 알아둬야 합니다.

### callbacks를 통한 비동기 결과

예시로 Node.js를 들 수 있습니다. 아래는 예시입니다.

``` js
// Node.js
fs.readFile('myfile.txt', { encoding: 'utf8' },
    function (error, text) { // (A)
        if (error) {
            // ...
        }
        console.log(text);
});
```

### Continuation-passing style(CPS)

다음 단계가 명시적으로 매개 변수로 전달되는 것을 CPS라 부릅니다. 아래의 코드를 봅시다.

``` js
console.log('A');
identity('B', function step2(result2) {
    console.log(result2);
    identity('C', function step3(result3) {
       console.log(result3);
    });
    console.log('D');
});
console.log('E');

// Output: A E B D C

function identity(input, callback) {
    setTimeout(function () {
        callback(input);
    }, 0);
}
```

이 중첩된 함수는 콜백 지옥이라고도 부르는데, 중첩을 피하는 방법도 있습니다. 아래는 이 중첩을 피하는 방법입니다.

``` js
console.log('A');
identity('B', step2);
function step2(result2) {
    // The program continues here
    console.log(result2);
    identity('C', step3);
    console.log('D');
}
function step3(result3) {
   console.log(result3);
}
console.log('E');
```

### 콜백의 장단점

CPS는 기본적인 메커니즘을 이해하기 쉽지만, 단점도 있습니다.

- 에러 처리가 더 복잡해졌다: 에러를 처리하는 방법은 두 가지가 있다. 콜백 함수를 이용하는 방법과 예외를 이용하는 것 2가지가 있다. 그러므로 이 두 가지를 동시에 사용할 경우 조심해야 한다.
- 코드 직관성이 떨어진다: 동기 함수에서는 입력(매개변수)과 출력(함수 결과) 사이에 구분이 명확하다. 콜백을 사용하는 비동기 함수에서는 이 두 개가 섞여 있다. 함수의 매개변수 중 몇 개는 입력을 위해, 나머지는 출력을 위해 사용된다.
- 구성이 더 복잡해졌다: 출력을 위한 개념이 함수의 매개변수에 존재하기 때문에 구성이 더 복잡해졌다

Node.js 콜백스타일은 3가지 단점이 있습니다.

- 에러를 처리하기 위한 if문이 많다.
- 에러 핸들러를 재사용하기 힘들다.
- 또한, 기본 에러 핸들러를 제공하는 게 더 어렵다. 기본 에러 핸들러는 당신이 함수를 호출 할 때 자신만의 핸들러를 작성하지 않을 때 유용합니다. 또한 만약 호출자가 특정한 핸들러를 지정하지 않는다면 함수에 의해 사용될 수도 있다.

-- 참고자료  
http://webframeworks.kr/tutorials/translate/es6-async/  
