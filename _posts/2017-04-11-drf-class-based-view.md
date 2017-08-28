---
layout: post
section-type: post
title: DRF - 클래스 기반 Views
category: DRF
tags: [ 'DRF', 'django-rest-framework' ]
---

> 본 문서는 [DRF 공식 문서](http://www.django-rest-framework.org/api-guide/views/#class-based-views)를 번역한 문서입니다.

## 클래스 기반 뷰

" django 클래스 기반 view는 예전 스타일의 view로 부터 시작하는 것을 환영한다." -  Reinout van Rees  

REST framework는 APIView class를 제공하는데, 이는 Django의 View 하위 클래스 입니다. APIVIew class들은 일반적인 View 클래스들과는 다음과 같이 다릅니다:

- handler methods에게 전달된 request들은 REST framework의 request instance들이 되고, 이것은 django의 HttpRequest가 아닙니다.
- Handler method들은 REST framework의 Response가 return될 것입니다. HttpResponse와는 다릅니다. 뷰는 내용물 절충과 응답에 올바른 렌더러를 설정하는 것을 관리 할 것입니다.
- 어떤 APIException 예외는 적절한 응답으로 감지하고 조정됩니다.
- 들어오는 request들은 인증을 하게 되며 적절한 권한과 throttle 확인들을 request가 handler method에 전송되기 전에 실행 될 것입니다.

APIView를 사용하는 것은 일반적인 view class를 사용하는 것과 좀 많이 비슷한데, 대개의 경우, 들어오는 request는 .get()이나 .post() 같은 적절한 handler method에 전송됩니다. 게다가, 많은 속성들은 API 정책의 다양한 관점을 제어하는 클래스들에 위치될 것 입니다.

예를 들면:
``` python
from rest_framework.views import APIView
from rest_framework.response import Response
from rest_framework import authentication, permissions

class ListUsers(APIView):
    """
    View to list all users in the system.

    * Requires token authentication.
    * Only admin users are able to access this view.
    """
    authentication_classes = (authentication.TokenAuthentication,)
    permission_classes = (permissions.IsAdminUser,)

    def get(self, request, format=None):
        """
        Return a list of all users.
        """
        usernames = [user.username for user in User.objects.all()]
        return Response(usernames)
```
