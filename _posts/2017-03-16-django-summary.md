---
layout: post
section-type: post
title: django 참고 하면 좋은 것들
category: django
tags: [ 'django', 'python' ]
---

# 용어 정리

- 장고 앱 : 프로젝트의 한 기능을 표현하기 위해 디자인 된 작은 라이브러리.
- 서드 파티 장고 패키지 : 파이썬 패키지 도구들에 의해 패키지화된, 재사용 가능한 프로그인 형태로 이용 가능한 장고 앱.

각 앱은 주어진 임무에만 집중할 수 있어야 한다. Single Resposibillity와 비슷한 개념.
앱 이름은 한 단어로만 작성하자.

### 특정 setting을 바탕으로 명령 실행

cli에서 특정 setting을 바탕으로 명령을 실행(그냥 하나만 사용했을경우 시크릿키, api키, aws키 같은 값들이 세팅파일에 위치하게 때문에)

mysite/setting/localsetting.py 가 있다고 할때

``` shell
$ python manage.py shell --settings=mysite.setting.localsetting
```

이는 환경변수를 이용해서도 해결이 가능하다.

### 환경변수 세팅하기

#### MAC / LINUX

~/.bashrc 또는 ~/.bash_profile, ~/.zshrc에 ```export SECRET_KEY=1C34-32324-324``` 를 추가하는 식으로 세팅하고, source ~/.bashrc로 적용시킨다.

#### CircleCI

[circleCI 공식 문서](https://circleci.com/docs/1.0/environment-variables/)를 참고하자.


### 환경변수 가져오기 / 비밀키가 존재하지 않을 때 예외처리 (python)

``` python

import os
from django.core.exceptions import ImproperlyConfigured

def get_env_variable(var_name):
  try:
    # 환경변수 값를 반환
    return os.environ[var_name]
  except KeyError:
    error_msg = "Set the {} environment variable".format(var_name)
    raise ImproperlyConfigured(erro.r_msg)
```

### 여러개의 requirements.txt 이용하기

환경에 따른 설치 컴포넌트들을 분리하는 방법을 보자.

다음과 같은 트리구조로 requirements 디렉터리를 만든다
``` text
requirements/
  base.txt
  local.txt
  ci.txt
  staging.txt
  production.txt
```

- base.txt 에는 모든 환경에 걸쳐 공통으로 이용할 의존성 모듈을 넣어준다.(django, django-restframework 등)
- local.txt에는 개발환경에 따른 패키지를 작성한다.(-r base.txt를 추가해야 한다.)
- ci.txt는 ci에 따른 패키지를 작성한다.(-r base.txt를 추가)

### setting에서 파일 경로 처리하기
``` python
MEDIA_ROOT = "/User/jang/projects/"
```

위와같이 경로를 절대경로로 넣는 실수는 하지말자.  

아래의 ```os.path.join(BASE_DIR,"filename")```을 사용해서, 경로를 설정해주자

``` python
# Build paths inside the project like this: os.path.join(BASE_DIR, ...)
 BASE_DIR = os.path.dirname(os.path.dirname(os.path.abspath(__file__)))
```

## 쿼리와 데이터베이스 레이어에서 유의할 점

- 단일 객체를 가져와 작업을 하는 세부 페이지 같은 뷰에서는 단일 객체에서 get_object_or_404() 이용(뷰에서만 이용한다!!), 이는 예외처리를 할 필요가 없다.
- 다른 get()함수는 ObjectDoesNotExist 혹은 DoesNotExist 예외를 사용해 예외처리한다.
- 여러개의 오브젝트가 리턴되는 예외는 MultipleObjectsReturned 예외를 사용해 예외처리한다.
- 쿼리를 명확하게 하기 위해 lazy evaluation을 이용해서 코드를 더 깔끔하게 한다.(너무 긴 쿼리문은 가독성을 하락시킨다.)
- [쿼리 표현식](https://docs.djangoproject.com/en/1.10/ref/models/expressions/)을 익혀두고 활용하자.
- 로우 SQL(mysql문 직접 사용 등)사용은 지양하자.
- 필요에 따라 인덱스를 활용하자.(db_index=True를 활용)
- HTTP를 트랜잭션(HTTP의 다중 요청으로 인한 버그를 막기 위해서 데이터베이스 업데이트를 단일화 처리하는 것.) 처리하자.
