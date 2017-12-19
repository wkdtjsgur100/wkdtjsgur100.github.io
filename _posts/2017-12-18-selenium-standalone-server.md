---
layout: post
section-type: post
title: Selenium remote webdriver를 사용한 크롤링
category: selenium
tags: [ 'selenium' ]
---

# selenium remote webdriver

webdriver를 local에서 실행시키는 대신 특정 원격 서버에서 실행시킬 수 있는 webdriver를 말합니다. 이를 이용해 동시에 여러 머신에서 자동화 테스팅을 수행할 수 있고, distribute 환경에서 유용하게 쓰입니다.  

> 제 경우엔 urllib.error.URLError: <urlopen error [Errno 111] Connection refused> 이 에러를 해결하기 위해서 remote webdriver를 사용했습니다.

## 장점

- 브라우저가 있는 곳으로부터 테스트가 실행되는 위치를 구분합니다.
- 현재 OS에서 사용할 수 없는 브라우저로 테스트를 실행할 수 있습니다 (브라우저가 다른 위치에 있을 수 있기 때문에).

## 단점

- 실행 중인 외부 servlet container가 필요합니다.
- 원격 서버에서 텍스트를 가져올 때 라인 끝과 관련된 문제가 발견될 수 있습니다.
- 특히 예외가 발생할 때 테스트에 추가 시간을 도입합니다.

# Selenium standalone server

> The Selenium Server is needed in order to run Remote Selenium WebDriver. Selenium 3.X is no longer capable of running Selenium RC directly, rather it does it through emulation and the WebDriverBackedSelenium interface.(출처: [Selenium 홈페이지](http://www.seleniumhq.org/download/))

```  
번역 : selenium server는 Remote Webdriver를 실행하기 위해 필요합니다. selenium 3.X 는 더이상 Selenium RC를 직접적으로 구동할 수 있지 않습니다. 대신 emulation과 WebDriverBackedSelenium 인터페이스를 통해서 구동할 수 있습니다.
```

## selenium standalone server 실행

[selenium standalone server 다운로드 페이지](http://www.seleniumhq.org/download/)
에 최신 버전을 다운 받습니다.

다음과 같은 명령어로 standalone server를 실행 시킬 수 있습니다.

``` shell
java -jar selenium-standalone-server-3.8.1.jar
```

background에서 실행시키고 싶을 경우, nohup을 사용합니다.

``` shell
nohup java -jar selenium-standalone-server-3.8.1.jar &
```

ubuntu의 경우, xvfb를 실행시켜 줘야 합니다. nohup을 사용해서, background에서 작동하도록 해주겠습니다.

``` shell
# xvfb 설치
sudo apt-get install xvfb
# xvfb 실행(in background)
nohup Xvfb :99 &
```

# remote webdriver 사용하기

아래와 같은 코드를 사용해서, remote webdriver를 사용해 크롤링할 수 있습니다.

``` python
from selenium import webdriver

from selenium.webdriver.common.desired_capabilities import DesiredCapabilities

driver = webdriver.Remote('http://127.0.0.1:4444/wd/hub', DesiredCapabilities.FIREFOX)

driver.get('https://www.naver.com')

driver.quit()
```

- 출처  
[selenium wiki](https://github.com/SeleniumHQ/selenium/wiki/RemoteWebDriver)
