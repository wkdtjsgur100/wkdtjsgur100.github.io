---
layout: post
section-type: post
title: Facebook Login Test 1 ( CircleCI + Selenium + Python3.6 )
category: ci
tags: [ 'circle-ci', 'facebook api', 'ci', 'test' ]
---

> CircleCI를 이용해 Facebook Login을 테스트 하는 과정을 단계 별로 기술해 보았음

## 로컬에서의 환경 세팅(python 3.6.0)
1) 가장 먼저, Facebook Login 테스트 을 위해 python 환경 설정부터 해보자
일단  [이 링크](https://cjh5414.github.io/python-%EA%B0%9C%EB%B0%9C%ED%99%98%EA%B2%BD%EA%B5%AC%EC%B6%95/)
를 보고 pyenv + virtualenv + autoenv 를 설정한다.
테스트가 잘 되고 있는지 확인하기 위해, 로컬에서의 환경과 circle-ci 내부 환경을 맞춰 주는 것이 좋다.

2) 필요한 python package를 깔아주고, pip freeze > requirements.txt를 이용해 requirements 파일을 만들자

``` text
//requirements.txt
pytest==3.0.5
selenium==2.53.6
```
3) 일단 로컬에서 testing이 잘 되는지 확인하기 위해 아래와 같은 파일을 추가한다.

``` Python3
  #selenium-browser.py
  from selenium import webdriver

  def test_selenium():
      driver = webdriver.Firefox()
      driver.get("http://localhost:8000")

      assert driver.title == 'Directory listing for /'

      driver.close()
```

http://localhost:8000 을 돌리기 위해, ```python -m http.server 8000 &```
을 실행한 후,
위 소스를 넣고 pytest selenium-browser.py를 해보면, 테스팅이 잘 되는 것을 알 수 있다.
__이제 ci를 붙여보자__

## 위의 상태에서 CIRCLECI를 추가로 붙여보자

1. 같은 경로에 circle.yml 파일을 만든다.
circleci configuration에 대한 내용은
[이곳]({% post_url 2017-01-16-circleci-yml %})
을 참고하자

2. circle.yml을 작성하자.

``` text
machine:
  pre:
    - cd /opt/circleci/.pyenv; git pull    
  python:
    version: 3.6.0

dependencies:
  pre:
    - pip install -r requirements.txt
  post:
    - python -m http.server 8000:
        background: true
test:
  override:
    - pytest selenium-browser.py
```

위와 같은 내용으로 circle.yml을 작성하게 되었는데, 각 줄에 대해서 유의해야 할 부분만 설명을 해보면,

 ```cd /opt/circleci/.pyenv; git pull``` << 이 부분은 현재 circleci에서 python 3.6.0버전을 명시 해 줄 경우 에러를 해결하기 위해 써놓은 명령어 이다. [ [참고 링크](http://stackoverflow.com/questions/41381930/use-python-3-6-0-in-circleci) ]

 ```- python -m http.server 8000:``` << 이 부분은 원격 서버를 돌리기 전 local에서 테스트하기 위해 임시로 서버를 돌린 것이고, 서버를 돌릴땐 background에서 작업해야 하기 때문에 ```background: true``` 코드를 추가했다.

> [이 링크 ](http://stackoverflow.com/questions/33426220/webdriver-container-in-circleci-for-testing/33433405#33433405)를 보면 xvfb및 chromedriver가 기본적으로 설치되어 있는 것을 알 수 있기 때문에, selenium을 사용한다고 해도 pyvirtualdisplay같은 것들은 설치하지 않아도 좋다.
