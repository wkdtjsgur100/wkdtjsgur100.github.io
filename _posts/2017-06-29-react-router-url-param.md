---
layout: post
section-type: post
title: react router에서 url을 통해 parameter를 받기(react-router-dom, v4)
category: react-router
tags: [ 'react', 'react-router' ]
---

먼저, react-router에서

``` js
// route.js
<Router>
    <div>
        <Switch>
            <Route path="/" component={App}/>
        </Switch>
    </div>
</Router>
```

다음과 같이 routing을 하게 되면 App이라는 Component가 나오게 된다.  
그런데 App Component에서 url의 정보를 가져오고 싶으면 어떻게 해야하는지 살펴보자.  

라우팅을 하는 곳에서는, 다음과 같이 정보를 준다고 가정하자.

``` js
<Route path="/:testvalue" component={App}/>
```

App Component에서는, 다음과 같이 하면 된다.  

``` js
// App.js
const App = ({match}) => (
    <div>
        {match.params.testvalue}
    </div>
)
```

이렇게 되면 url에 ```localhost:3000/testtest```다음과 같이 입력했을 때, 결과 값은 testtest가 나오게 된다.
