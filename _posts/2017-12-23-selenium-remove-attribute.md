---
layout: post
section-type: post
title: Selenium에서 element의 특정 속성(attribute) 제거하기(readonly, onchange, disabled 등)
category: selenium
tags: [ 'selenium' ]
---

> element의 readonly나 disabled, onchange 같은 특정 attribute를 제거하고 크롤링을 하고 싶을 때, 간단한 자바스크립트 구문을 이용해 제거할 수 있습니다. 

## 속성(Attribute) 제거하기

selenium webdriver에는 `execute_script`라는 자바스크립트를 실행할 수 있는 함수가 있습니다.  
자바스크립트의 `removeAttribute` 함수를 이용해 속성을 제거할 수 있습니다.  
  
예를 들어, 다음과 같은 element가 있다고 가정해보겠습니다.  

``` html
. . .
<input type="text" onchange="filtering();" id="readonly_elem" value="readonly" readonly />
. . . 
```

`onchange` 속성과 `readonly` 속성을 다음과 같이 제거할 수 있습니다.

``` python
driver = webdriver.Firefox()

# readonly와 onchange를 가지고 있는 엘리먼트를 가져옵니다.
elem = driver.find_element_by_id('readonly_elem')

# onchange, readonly 속성을 제거합니다.
driver.execute_script(
        'arguments[0].removeAttribute("onchange")', elem)
driver.execute_script(
        'arguments[0].removeAttribute("readonly")', elem)
```

