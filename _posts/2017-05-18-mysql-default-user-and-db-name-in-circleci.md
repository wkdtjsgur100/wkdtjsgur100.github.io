---
layout: post
section-type: post
title: CircleCI에서 사용하는 default mysql DB Name, User(Ubuntu 14.04)
category: circleci
tags: [ 'circleci', 'ci' ]
---

django에서 DB를 mysql로 변경 했을 때, 나는 다음과 같이 setting.py를 설정 했었다.

``` python
DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.mysql',
        'NAME': '이름',
        'USER': '유저',
        'PASSWORD': 'password',
        'HOST': 'localhost',
        'PORT': '',
    }
}
```

하지만 위의 코드대로 circleCI를 돌렸더니 해당하는 DB이름을 찾을 수 없다고 에러가 떴다.  
또한 User도 마찬가지로 없다고 떴다.  
그래서 구글링으로 삽질해 본 결과, 다음과 같은 circleCI Employee의 답변을 찾을 수 있었다.  

![StackOverflow](/images/posts/answer-ci.png)

levlaz님의 조언대로, 다음과 같이 설정을 바꾸고 실행해보니

``` python
DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.mysql',
        'NAME': 'circle_test',
        'USER': 'ubuntu',
        'PASSWORD': '',
        'HOST': 'localhost',
        'PORT': '',
    }
}
```

매우 잘 작동하는 것을 확인할 수 있었다.  
