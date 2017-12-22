---
layout: post
section-type: post
title: selenium에서 임의의 ip로 크롤링하기 (python, Ubuntu, Firefox)
category: selenium
tags: [ 'selenium' ]
---

> selenium으로 크롤링을 하다보면 ip를 막아버리는 사이트도 있습니다. 이를 피하기 위해 [Tor](https://ko.wikipedia.org/wiki/%ED%86%A0%EC%96%B4)를 이용해, selenium에서 임의의 ip로 크롤링하는 방법에 대해서 작성했습니다.(Ubuntu, Firefox)

# 구동 환경

제 구동환경은 다음과 같습니다.(참고)

- Ubuntu 16.04
- selenium 3.8.0
- python 3.6.0
- firefox 57.0.1
- geckodriver 0.19.1

# 임의의 ip로 크롤링하기

## Tor 설치

``` shell
sudo apt-get update
sudo apt-get install tor
/etc/init.d/tor restart
```

먼저, [Tor](https://ko.wikipedia.org/wiki/%ED%86%A0%EC%96%B4)를 설치해줍시다.

``` shell
$ netstat -ntlp
Proto Recv-Q Send-Q Local Address           Foreign Address         State       PID/Program name
tcp        0      0 0.0.0.0:22              0.0.0.0:*               LISTEN      -
tcp        0      0 127.0.0.1:9050          0.0.0.0:*               LISTEN      -
tcp6       0      0 :::22                   :::*                    LISTEN      -
```

`netstat -ntlp`로 확인해보면, 다음과 같이 9050 포트에 [SOCKS](https://ko.wikipedia.org/wiki/SOCKS) 리스너가 만들어진 것을 확인하실 수 있습니다.

## WebDriver에서의 ip 우회

먼저, 기존의 평범한 RemoteWebDriver를 사용해 현재 ip를 확인해보겠습니다.  

``` python
from selenium import webdriver

driver = webdriver.Firefox()

driver.get('http://icanhazip.com/')

print(driver.page_source)

driver.quit()
```

`icanhazip.com`이라는 사이트는 현재 ip를 내부에 출력해주는 아주 심플한 사이트 입니다. page_source를 출력해, 현재 ip를 출력해주었습니다.

``` python
<html><head><link rel="alternate stylesheet" type="text/css" href="resource://content-accessible/plaintext.css" title="Wrap Long Lines"></head><body><pre>
13.125.XX.XXX
</pre></body></html>
```

실행 결과는 위와 같습니다. ip가 제 경우엔 13.125.로 시작하는 ip가 할당이 되었네요.  

이제 Tor 설치과정에서 열어놓았던 9050포트를 활용해서, ip를 우회시키도록 하겠습니다.

``` python
from selenium import webdriver


profile = webdriver.FirefoxProfile()
profile.set_preference("network.proxy.type", 1)
profile.set_preference("network.proxy.socks", "127.0.0.1")
profile.set_preference("network.proxy.socks_port", 9050)

profile.update_preferences()

driver = webdriver.Firefox(profile)

driver.get('http://icanhazip.com/')

print(driver.page_source)

driver.quit()
```

위와 같이 소스를 작성하고 실행해보면,

``` shell
. . .  
<span class="cf-footer-item"><span data-translate="your_ip">Your IP</span>:
77.247.181.162
</span>
. . .  
```

`77.247.181.162`로 ip가 바뀐것을 확인할 수 있습니다!


## RemoteWebDriver에서의 ip 우회

RemoteWebDriver가 무엇인지 모르신다면 [이 링크](https://wkdtjsgur100.github.io/selenium-standalone-server/)를 참고하시면 됩니다.  

``` python
from selenium.webdriver.common.desired_capabilities import DesiredCapabilities

. . .
# 나머지 코드는 WebDriver에서의 ip 우회와 완전히 같습니다.
driver = webdriver.Remote('http://127.0.0.1:4444/wd/hub', DesiredCapabilities.FIREFOX, browser_profile=profile)

. . .
```

WebDriver와의 차이는, FireFoxProfile을 넘길 때 이렇게 위와같이 넘겨 주기만 하면 됩니다.
