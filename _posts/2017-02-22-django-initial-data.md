---
layout: post
section-type: post
title: (Django) 모델에 initial data를 넣는 방법 (fixture, RunPython, RunSQL)
category: django
tags: [ 'django', 'python', 'ORM' ]
---

> [장고 공식 문서](https://docs.djangoproject.com/en/1.10/howto/initial-data/)를 참고해 작성

# fixture를 사용

fixture란 django가 DB에서 import하는 방식을 알고 있는 데이터들의 집합이라고 한다.(이게 뭔소리지)
이미 몇몇 데이터를 가지고 있고 fixture를 생성하는 가장 직관적인 방법은 ```manage.py dumpdata``` 명령어를 사용하면 된다.  
fixture는 json,xml,yaml으로 작성될 수도 있다.

예를 들면, Person model의 이런 json 문서가 있을 수 있다.

``` json
[
  {
    "model": "myapp.person",
    "pk": 1,
    "fields": {
      "first_name": "John",
      "last_name": "Lennon"
    }
  },
  {
    "model": "myapp.person",
    "pk": 2,
    "fields": {
      "first_name": "Paul",
      "last_name": "McCartney"
    }
  }
]
```

yaml은 이런 식이 될 수 있다.  

```
- model: myapp.person
  pk: 1
  fields:
    first_name: John
    last_name: Lennon
- model: myapp.person
  pk: 2
  fields:
    first_name: Paul
    last_name: McCartney
```

이 데이터들을 로딩하는 방법은 간단하다.

``` text
manage.py loaddata <fixturename>
```

를 사용하면 되고, <fixturename>은 생성한 inital-data의 파일이 되겠다.
loaddata를 실행할 때마다 데이터는 fixture에서 읽혀질꺼고 DB로 다시 로드된다.
이는 fixture에서 생성된 열중 하나를 바꾸고 다시 loaddata를 실행한다면 예전에 만들었던 그 파일을 제거할 것이란 걸 알아두자.

# migrations으로 initial-data 넣기

fixture를 사용하지 않고 자동적으로 initial-data를 넣고 싶다면 RunPython 혹은 RunSQL을 이용해 앱의 migration을 생성하자.

## RunPython

```
class RunPython(code, reverse_code=None, atomic=None, hints=None, elidable=False)
```

code는 두개의 인자를 받는 호출 가능한 객체가 되어야한다; 첫 번째 인자는 프로젝트 이력에서 운영되되었던 이전의 모델들이 포함된 django.apps.registry,Apps의 인스턴스이고, 두 번째 인자는 SchemaEditor의 인스턴스다.

reverse_code 인자는 migrations을 요청하지 않았을 때 호출된다. 이 인자는 code에서 완료된 것을 취소해서 migration을 되돌릴수 있도록 해야한다.  

hint인자는 allow_migrate() 함수에 ```**hints```로 전달된다. allow_migrate() 메서드는 DB 루틴 결정에 도움을 주는 database router들의 메서드라 한다.

또 elidable 인자는 squashing migrations 단계에서 operation이 제거될 것인지 결정한다.

migration file에서 migration class 위에 따로 함수를 분리해서 코드를 작성해서 RunPython에 이를 전달하는 것이 권장된다. 아래는 예제

``` python
# -*- coding: utf-8 -*-
from __future__ import unicode_literals

from django.db import migrations, models

def forwards_func(apps, schema_editor):
    # We get the model from the versioned app registry;
    # if we directly import it, it'll be the wrong version
    Country = apps.get_model("myapp", "Country")
    db_alias = schema_editor.connection.alias
    Country.objects.using(db_alias).bulk_create([
        Country(name="USA", code="us"),
        Country(name="France", code="fr"),
    ])

def reverse_func(apps, schema_editor):
    # forwards_func() creates two Country instances,
    # so reverse_func() should delete them.
    Country = apps.get_model("myapp", "Country")
    db_alias = schema_editor.connection.alias
    Country.objects.using(db_alias).filter(name="USA", code="us").delete()
    Country.objects.using(db_alias).filter(name="France", code="fr").delete()

class Migration(migrations.Migration):

    dependencies = []

    operations = [
        migrations.RunPython(forwards_func, reverse_func),
    ]
```

일반적으로 이것은 data migrations을 생성하고, 사용자 데이터 업데이트 및 변경을 실행하고, ORM 또는 Python 코드에 접근해야 하는 모든 것들을 수행하는 작업이다.

만약 South로 부터 업그레이드 하고 있다면, 이건 기본적으로 한 operation과 같은 South 패턴입니다 - ORM과 사용가능한 스키마 operations와 함께 앞 뒤로 하나 혹은 두개의 메서드. 대부분의 시간은, orm.Model 또는 orm["appname","Model"] 레


## RunSQL  
