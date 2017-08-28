---
layout: post
section-type: post
title: Selenium에서 특정 element가 갑자기 클릭이 되지 않을 때 (python)
category: selenium
tags: [ 'selenium', 'python' ]
---

selenium을 이용해서 functional test를 쨔는 도중, 특정 element를 클릭하는 부분을 쨔야하는 경우가 있었는데,

``` python
# 다음과 같이 시도해 보았다.
browser.find_element_by_xpath("//form[@class='ui form']/button").click()
```

아무리 해도 클릭이 되지 않는 것이었다.  

그래서 is_enabled()를 사용해서 해당 엘리먼트가 클릭 가능한 지 테스트 해봤지만 True를 반환해서 여러가지 방법을 시도해 보았는데,  
해당 버튼 element을 click()이나 submit() 함수를 이용해 클릭하는 것이 아닌, send_keys(Keys.ENTER)를 이용해 해당 버튼 element를 클릭하니 해결되었다.  

``` python
# 다음과 같이 해결
browser.find_element_by_xpath("//form[@class='ui form']/button").send_keys(Keys.ENTER)
```
