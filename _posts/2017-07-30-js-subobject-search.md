---
layout: post
section-type: post
title: javascript에서 object의 하위 object들을 탐색하는 방법
category: javascript
tags: [ 'javascript' ]
---

다음과 같은 object가 있다고 하자

``` js
test = {
    a: "tt",
    b: "tt2",
    c: "tt3",
}
```

test object 하위에 있는 모든 object들을 탐색하려고 할 때, 다음과 같은 코드를 작성하면 된다.

``` js
for(let key in test)
    console.log(test[key]);
```

콘솔에 출력되는 결과는 다음과 같다

``` shell
tt
tt2
tt3
```
