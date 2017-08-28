---
layout: post
section-type: post
title: django 토큰 인증
category: django
tags: [ 'django', 'token' ]
---

## 토큰 인증

권한 스키마는 간단한 토큰 기반의 HTTP 권한 스키마를 사용합니다. 토큰 인증은 클라이언트-서버 설정에 적절하고, native 데스크탑과 모바일 클라이언트 같은 것에 적합합니다.

TokenAuthentication 스키마를 사용하려면 TokenAuthentication를 포함하는 권한 [클래스들을 설정](http://www.tomchristie.com/rest-framework-2-docs/api-guide/authentication#setting-the-authentication-scheme)해야 합니다. 그리고 추가적으로 INSTALLED_APPS 설정에 rest_framework.authtoken을 추가해 줍니다:

``` python
INSTALLED_APPS = (
    ...
    'rest_framework.authtoken'
)
```

그리고 user를 위해서 token을 만드는 것을 필요로 합니다.
``` python
from rest_framework.authtoken.models import Token

token = Token.objects.create(user=...)
print token.key
```

클라이언트 권한 부여를 위해, token key는 HTTP header에 Authorization이 포함되어있어야 합니다. 이 key는 "Token" 이라는 문자열로 시작되어야 하고, 스페이스로 구분된 두개의 문자열이어야 합니다. 예를 들면:

```
Authorization: Token 9944b09199c62bcf9418ad846dd0e4bbdfc6ee4b
```

성공적으로 권한이 완료되면, TokenAuthentication은 다음과 같은 권한 정보를 제공합니다.
- request.user는 Django User instance가 될 것입니다.
- request.auth는 rest_framework.authtoken.models.BasicToken 인스턴스가 됩니다.

권한이 거부되어 인증되지 않은 응답은 적절한 WWW-Authenticate 헤더와 함께 HTTP 401 Unauthorized 응답을 결과로 반환합니다. 예를 들면:

```
WWW-Authenticate: Token
```

curl 명령어는 인증된 API 토큰을 테스트하는데 유용합니다. 예를 들면
```
curl -X GET http://127.0.0.1:8000/api/example/ -H 'Authorization: Token 9944b09199c62bcf9418ad846dd0e4bbdfc6ee4b'
```

> 참고 : 프로덕션에서 TokenAuthentication을 사용한다면 API가 https에서만 사용가능한 것인지 확인해야합니다.

## 토큰 생성하기

모든 User가 자동적으로 생성된 토큰을 가지고 있는 것을 원한다면, 간단히 User의 post_save 를 사용할 수 있습니다.

``` python
from django.conf import settings
from django.contrib.auth import get_user_model
from django.db.models.signals import post_save
from django.dispatch import receiver
from rest_framework.authtoken.models import Token

@receiver(post_save, sender=settings.AUTH_USER_MODEL)
def create_auth_token(sender, instance=None, created=False, **kwargs):
    if created:
        Token.objects.create(user=instance)
```

설치된 models.py 모듈에 코드 조각을 위치해야 하거나, django가 import할 다른 위치에 배치되어야합니다.  
만약 이미 몇몇 user들을 생성했다면, 다음과 같이 모든 존재하는 user들에게 토큰을 생성할 수 있습니다.  

``` python
from django.contrib.auth.models import User
from rest_framework.authtoken.models import Token

for user in User.objects.all():
    Token.objects.get_or_create(user=user)
```

TokenAuthentication을 사용할 때, 아마 주어진 username과 패스워드에 대한 토큰을 획득하기 위해서 클라이언트에게 메커니즘을 제공하길 원할것 입니다. REST framework는 이런 일을 하는 것을 제공하는 만들어진 뷰가 있습니다. 사용하려면, URLconf에 obtain_auth_token을 추가하세요

``` python
from rest_framework.authtoken import views
urlpatterns += [
    url(r'^api-token-auth/', views.obtain_auth_token)
]
```

obtain_auth_token view는 유효한 username과 패스워드가 폼 데이터 혹은 json을 사용해서 뷰에 POST되면 다음과 같은 JSON 응답을 반환합니다.

```
{ 'token' : '9944b09199c62bcf9418ad846dd0e4bbdfc6ee4b' }
```

기본 obtain_auth_token view는 명시적으로 JSON 요청과 응답을 사용합니다, 만약에 obtain_auth_token 뷰의 버전을 커스텀하고 싶다면, urlconf에서 사용하는 것 대신 ObtainAuthToken view 클래스를 오버라이딩해서 할 수 있습니다.
