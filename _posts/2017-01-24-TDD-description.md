---
layout: post
section-type: post
title: python + django + circleci를 활용한 TDD(Test-Driven Development) 요약 (1)
category: TDD
tags: [ 'TDD', 'test', 'python']
---

우리는 테스트 고트(염소)에게 복종한다. 한낱 염소일 뿐이지만, 이 염소는 생각보다 민첩해서 높은 곳에서 조금씩 내려와 땅을 밟는 그런 염소이다.  
이 염소가 하는 말을 잘 듣고, 따라하자

우린 실패하는 것을 두려워 하지 말아야 한다. 일단 쓰고, 실패하고, 수정한다.

먼저 사용자 테스트를 위해 다음과 같은 코드를 작성하자

``` python
from selenium import webdriver

browser = webdriver.Chrome('./chromedriver')
browser.get('http://localhost:8000')

assert 'Django' in browser.title

browser.quit()
```

python 가상 환경을 위해, virtualenv, autoenv를 사용해서 환경을 셋팅하자

``` text
$ pyenv virtualenv [Python Version] [new virtualenv name] # 새로운 가상환경 생성
$ pyenv versions
* system (set by /Users/jihun/.pyenv/version)
  3.5.2
  [new virtualenv name]

$ touch .env   # autoenv
$ echo "pyenv activate [new virtualenv name]" > .env
$ pip install selenium # selenium 설치
$ pip install django # django 설치

```

python 환경을 저장하기 위해서, requirements.txt를 만들어서 저장해 놓자.

``` text
$ pip freeze > requirements.txt
```

git에 잘못된 파일을 commit list에 넣었을 경우, ```$ git rm -r --cached [file name]```를 사용해 해결합니다.

# Function Test를 이용한 최소 기능의 애플리케이션 설계

셀레늄을 이용한 테스트에서는 실제 웹 브라우저를 실행해서 애플리케이션이 어떻게 동작하는지 사용자 관점에서 확인 할 수 있다. 이런 테스트를 기능테스트(functional test, FT)라고 부른다.

사용자 스토리란 특정 기능을 사용자가 어떻게 사용하며 이때 애플리케이션이 어떻게 반응해야 하는지를 확인하는 방식이다.

FT는 사람이 이해할 수 있는 스토리를 가지고 있어야 하기 때문에, 테스트 코드에 주석을 기록한다. 새로운 FT를 만들 때는 주석을 먼저 작성한다.

애플리케이션 요구사항과 특징을 FT를 보고 논의 할 수 있을 정도가 되어야 한다.




-- 출처 <br>
(책) 파이썬을 이용한 클린 코드를 위한 테스트 주도 개발
