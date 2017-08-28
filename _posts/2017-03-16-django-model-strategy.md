---
layout: post
section-type: post
title: Django model에서 유의해야 할 점.
category: django
tags: [ 'django', 'python' ]
---

## 모델을 이용할 때 유의해야 할 점

1. 모델이 너무 많으면 앱을 나눈다.
    - 각 앱이 모델이 5개가 넘으면 앱을 나누는 것을 권장
2. 모델 상속에 주의하자.
    - 모델 사이에서 중복되는 내용이 최소라면 모델의 상속자체가 필요없다. 그냥 모델 두곳에 필드를 추가하자.
    - 모델 사이에 중복된 필드가 혼란을 야기하거나 의도치 않은 실수를 유발할 정도로 많을때, 추상화 기초 모델(abstract base class)로 이전 될 수 있게 리팩터링한다.
    - proxy 모델은 종종 편리하게 이용되지만 방식이 좀 다르다.

### 실제로 모델 상속해보기

``` python
from django.db import models

class TImeStampedModel(models.Model):
    created = models.DateTImeField(auto_now_add=True)
    modified = models.DateTimeField(auto_now = True)

    # 아래의 두줄이 TImeStampedModel을 abstrat base class로 만들어준다.
    # 마이그레이션 할때 테이블이 생성되지 않게된다.
    # 아래 두줄을 넣게 되면, 이 class를 상속받는 모든 클래스들이 자신만의 타임스탬프 필드를 가지게 된다.
    class Meta:
        abstract = True
```

> 마이그레이션 개수에 연연하지 말고, 만약 너무 많다면 squashmigrations을 이용해보자.

## 장고에서의 모델 디자인 전략

### 데이터베이스 정규화 하기

[위키피디아 글](https://ko.wikipedia.org/wiki/%EB%8D%B0%EC%9D%B4%ED%84%B0%EB%B2%A0%EC%9D%B4%EC%8A%A4_%EC%A0%95%EA%B7%9C%ED%99%94)을 읽고 데이터베이스 정규화에 대한 이해를 바탕으로 모델을 디자인 한다.

### 반드시 필요한 경우에만 비정규화를 하자.

웬만하면 비정규화는 사용하지 말자.

### 언제 널을 쓰고 언제 공백을 쓸 것인가

Two Scoops of django 책의 p.64를 참고

### 언제 BinaryField를 사용할 것인가

BinaryField는 exclude, filter등 기타 sql문이 적용되지 않는다.
다음과 같은 경우에 BinaryField를 사용한다.

- 메세지 팩 형식의 콘텐츠
- 원본 센서 데이터
- 압축된 데이터

바이너리 데이터는 크기가 방대할 수 있고, 데이터베이스가 느려질 수 있다는 점을 감안하자.

### generic relation는 피하자

- generic relation이 필요하다면, 모델 디자인을 새롭게 바꿔 해결할 수 있는지 확인해 보자.
- 불가피하다면 서드파티 앱 사용을 고려해 보자.

### Model의 ```_meta``` api

장고 모델의 데이터를 조정하거나 변경할 수 있는 관리 도구를 제작할때, 모델 필드의 리스트를 가져올때, 특정 필드의 클래스를 가져올 때 등에 사용된다.  
자세한 사항은 [장고 공식문서](https://docs.djangoproject.com/en/1.10/ref/models/meta/)를 확인하자.

### 모델 매니저

장고 모델에서 MyModel을 생성했을때,

``` python
class MyModel(models.Model):
    name = models.CharField(max_length=255)
```

위와 같은 모델이 있으면, 보통 우리는

``` python
MyModel.objects.create(name='장선혁')
```

을 통해서 DB를 컨트롤 한다. 그런데 objects를 개인적으로 customize시켜 DB를 다루는 것을 개인화 시킬 수 있는데,  
이것을 모델 매니저를 통해 제작할 수 있다. 아래는 예제이다.

``` python
class MyModelManager(models.Manager):
    use_related_fields = True

    def published(self, **kwargs):
        return self.filter(pub_date__lte = timezone.now(), **kwargs)

class MyModel(models.Model):
    name = models.CharField(max_length=255)
    objects = MyModelManager()
```

이제 아래와 같은 코드를 사용하면, 커스터마이징된 DB 컨트롤을 할 수 있게 된다.

``` python
MyModel.objects.published.create(name='장선혁')
```

위의 예제보다
``` python
MyModel.my_objects.create(name='장선혁')
```

을 사용하고 싶은 경우가 종종 있지만, 이는 매우 유의해서 사용해야 한다.  
모델을 상속받아 이용할 때의 문제와, 쿼리셋을 예상할 수 없게 만드는 문제 때문이다.

### 거대 모델(fat model) 이해하기

거대 모델(fat model) 이란 데이터 관련 코드를 뷰나 템플릿에 넣기 보다는 모델에 넣어서 캡슐화 하는 것이다.  
이 방법은 프로젝트 전체에서 코드의 재사용을 개선할 수 있는 최고의 방법이다.  

이 방법의 문제는 신의 객체(god object) 수준으로 모델이 거대해 졌을 때 문제가 생기는데, 이를 개선할 수 있는 방법에는 모델 행동(믹스인), 상태없는 헬퍼 함수, 모델행동과 헬퍼 함수 등이 있다.
