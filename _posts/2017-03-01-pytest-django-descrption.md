---
layout: post
section-type: post
title: pytest-django
category: pytest
tags: [ 'pytest', 'pytest-django' ]
---

> [pytest-django 공식 문서](https://pytest-django.readthedocs.io/en/latest/)를 번역한 글입니다.

# Quick Start

``` text
$ pip install pytest-django
```

DJANGO_SETTINGS_MODULE 가 정의되어 있는지와 우리 테스트를 발견할 수 있도록 해야한다.
([테스트를 감지하지 못할 때](https://pytest-django.readthedocs.io/en/latest/faq.html#faq-tests-not-being-picked-up)를 참고)

``` text
# -- FILE: pytest.ini (or tox.ini)
[pytest]
DJANGO_SETTINGS_MODULE = test_settings
# -- 권장되는 옵션:
python_files = tests.py test_*.py *_tests.py
```

pytest로 테스트를 돌리면 끝

``` text
$ pytest
```

# 왜 Django manage.py test 대신 사용할까?

Django 표준 test 메커니즘에 나타나지 않는 pytest의 특별한 장점이 있다.

* 적은 boilerplate(*boilerplate:변경없이 계속하여 재사용할 수 있는 것, 일종의 템플릿이라고도 할 수 있다.*): unittest를 import할 필요도, 서브클래스를 만들 필요도 없다. 그냥 테스트를 작성하면 된다.
* fixture들로 테스트 의존성(dependency)을 관리
* 데이터베이스 재사용 : 모든 테스트를 돌릴 때 테스트 데이터베이스를 다시 만들 필요가 없다.
* 속도 향상을 위해 다중 프로세스를 사용해서 테스트를 돌림.
* pytest 관련한 멋지고 많은 플러그인
* 손쉬운 전환 : 어떠한 변경없이도 이미 존재하는 unittest 스타일의 테스트들이 잘 작동한다.

pytest에 대한 자세한 정보는 [pytest documentation](http://docs.pytest.org/) 참고
