---
layout: post
section-type: post
title: pytest fixture
category: pytest
tags: [ 'pytest', 'fixture' ]
---

> python testing package인 pytest 중 fixture에 대해서 [pytest 공식 문서](https://docs.pytest.org/en/latest/fixture.html)를 참고해 작성했습니다.
>
> 왜 TestCase가 아니라 pytest를 사용하는 지에 대해서는 [왜 pytest 를 사용할까? 포스트](https://wkdtjsgur100.github.io/blog/2017/02/06/why-i-use-pytest/)를 참고하면 됩니다.

목차
- pytest fixtures: explicit, modular, scalable.
- 함수 인자들로서 사용되는 fixture들(Fixtures as Function arguments)
- "Funcargs" 종속성 대입의 대표적인 예
- 한 모듈(또는 클래스/세션)에서 테스트들을 거친 fixture를 공유하기
- fixture finalization / teardown 코드 실행

## pytest fixtures: explicit, modular, scalable.

pytest를 사용하는 목적은, 테스트들이 반복적으로 실행 가능하게 하는 고정된 baseline을 제공하는데에 있다. 이는 xUnit 스타일인 setup/teardown 함수보다 훨씬 더 향상된 기능들을 다음과 같이 제공합니다.

- fixture들은 명시적인 이름들을 가지고 있고, 테스트 함수, 모듈, 클래스 또는 전체 프로젝트들 에서 사용법을 선언함으로서 사용할 수 있습니다.
- fixture들은 각각의 fixture 이름이 다른 fixture들을 스스로 사용할 수 있는 fixture 함수를 부르는 것과 같이 모듈을 통한 방법으로 실행됩니다.
- fixture 관리는 간단한 유닛테스트에서, 설정과 컴포넌트 옵션에 따라서 테스트 하고 fixture parametrize를 하거나 클래스, 모듈, 또는 전체 테스트 세션 범위를 거쳐서 fixture를 재사용하는 것 같은 복잡한 기능 테스트로 확장합니다.

게다가, pytest는 classic xunit-style 설정을 지원합니다. 두가지 스타일을 섞을 수도 있고, 원한다면 클래식한 방법에서 점차 새로운 스타일로 옮길수도 있습니다. 또한 이미 존재하는 unittest.TestCase style 혹은 nose based 프로젝트들에서 부터 시작하는 것도 할 수 있습니다.

## 함수 인자들로서 사용되는 fixture들(Fixtures as Function arguments)

테스트 함수들은 입력 인자로 이름을 지어서 fixture 객체를 받을 수 있습니다. 각각의 인자 이름에, 그 이름으로 된 fixture 함수는 fixture 객체를 제공합니다. fixture 함수들은 ```@pytest.fixture``` 로 등록한 함수들입니다. 간단한 예제를 한번 봅시다.

``` python
# content of ./test_smtpsimple.py
import pytest

@pytest.fixture
def smtp():
    import smtplib
    return smtplib.SMTP("smtp.gmail.com")

def test_ehlo(smtp):
    response, msg = smtp.ehlo()
    assert response == 250
    assert 0 # for demo purposes
```

여기서 test_ehlo함수는 smtp fixture 값을 필요로 합니다. pytest는  smtp fixture 함수에 표시된 ```@pytest.fixture```를 발견하고 호출할 것입니다. 다음과 같이 테스트는 보여질 것입니다:

``` shell
$ pytest test_smtpsimple.py
======= test session starts ========
platform linux -- Python 3.5.2, pytest-3.0.7, py-1.4.32, pluggy-0.4.0
rootdir: $REGENDOC_TMPDIR, inifile:
collected 1 items

test_smtpsimple.py F

======= FAILURES ========
_______ test_ehlo ________

smtp = <smtplib.SMTP object at 0xdeadbeef>

    def test_ehlo(smtp):
        response, msg = smtp.ehlo()
        assert response == 250
>       assert 0 # for demo purposes
E       assert 0

test_smtpsimple.py:11: AssertionError
======= 1 failed in 0.12 seconds ========
```

여기 보이는 실패 트랙백 보고서를 보면 테스트 함수가 smtp 인자와 함께 호출된 것을 볼 수 있다. smtplib.SMTP() 인스턴스는 fixture함수에 의해서 생성된다. 테스트 함수는 의도적인 발생시킨 assert 0에서 실패합니다. 여기 다음과 같은 방법으로 테스트 함수를 호출하는 pytest에 의해 사용되는 올바른 통신 규약들이 있습니다.  

1. pytest는 test_ 접두어 때문에 test_ehlo 함수를 발견합니다. 그 test함수는 smtp으로 이름지어진 함수 인자를 필요로 합니다. fixture함수는 이와 매칭된 smtp로 이름지어진 fixture-marked함수를 찾아서 발견합니다.
2. smtp함수는 인스턴트를 만들도록 호출됩니다.
3. test_ehlo(<SMTP instance>)는 호출되고 test함수의 가장 마지막 줄에서 실패합니다.

함수 인자 스펠링이 틀렸거나 사용가능하지 않은 무언가를 사용하려 했다면 사용가능한 함수 인자들의 리스트들에서 에러를 볼수 있을 것입니다.

> 참고:  
> 다음과 같은 명령어로 사용가능한 fixture들을 볼 수 있습니다
> pytest --fixtures test_simplefactory.py

## "Funcargs" 종속성 대입의 대표적인 예

테스트 함수에 fixture를 넣을 때 pytest-2.0은 지금도 문서에 계속 표시되어 있는 funcargs또는 funcarg 메카니즘이란 용어를 소개합니다. 지금은 테스트 함수에서 인자들로서 fixture 값들을 주입하는 특정 경우를 언급하고 있습니다. pytest-2.3은 fixture들을 사용하는 가능성들이 좀 더 있지만 funcargsdms 직접적으로 test 함수의 종속성 상태를 허용하는 주요한 방법으로서 유지되고 있습니다.  

앞선 예제들이 좀 더 디테일한 것들을 보여줄 때, funcargs는 test 함수들이 특정한 미리 초기화된 어플리케이션 객체들에 비해서 import/setup/cleanup 디테일들을 신경쓰지 않고 쉽게 받고 작동하게 해줍니다. injector의 역할을 하고 fixture 객체들의 consumer들이 있는 테스트 함수가 있는 종속성 유입의 대표적인 예제가 여기 있습니다.

## 한 모듈(또는 클래스/세션)에서 테스트들을 거친 fixture를 공유하기

네트워크 접근을 필요로 하는 fixture들은 연경성에 의존하고 생성되는데 시간이 걸립니다. 이전의 예제를 확장해서, ```@pytest.fixture```에 ```scope='module'```을 붙여서 smtp fixture함수가 module당 한번 발생되도록 할 수 있습ㄴ디ㅏ. 한 테스트 모듈에서 여러 테스트 함수들은 앞서 말했던 것 같이 각각은 같은 smtp fixture 인스턴스를 받습니다. 다음 예제는 분리된 conftest.py 파일에서 fixture 함수를 만들어서 디렉터리에 있는 여러 테스트 모듈들로 부터 테스트들이 fixture 함수에 접근할 수 있도록 해줍니다.

``` python
# content of conftest.py
import pytest
import smtplib

@pytest.fixture(scope="module")
def smtp():
    return smtplib.SMTP("smtp.gmail.com")
```

fixture의 이름은 다시 smtp이고 fixture 함수 혹은 어떤 테스트에 매개변수를 입력해서 이름 smtp를 리스트화 시킴으로서 이 결과에 접근할 수 있게 됩니다.(conftest.py가 위치한 디렉터리 에)

``` python
# content of test_module.py

def test_ehlo(smtp):
    response, msg = smtp.ehlo()
    assert response == 250
    assert b"smtp.gmail.com" in msg
    assert 0  # for demo purposes

def test_noop(smtp):
    response, msg = smtp.noop()
    assert response == 250
    assert 0  # for demo purposes
```

우리는 일부러 assert 0 상태를 넣어서 실패한 다음 무슨 일이 일어나고 있는지 조사하고 지금 테스트들이 작동 될 수 있는지 검사합니다.

``` python
$ pytest test_module.py
======= test session starts ========
platform linux -- Python 3.5.2, pytest-3.0.7, py-1.4.32, pluggy-0.4.0
rootdir: $REGENDOC_TMPDIR, inifile:
collected 2 items

test_module.py FF

======= FAILURES ========
_______ test_ehlo ________

smtp = <smtplib.SMTP object at 0xdeadbeef>

    def test_ehlo(smtp):
        response, msg = smtp.ehlo()
        assert response == 250
        assert b"smtp.gmail.com" in msg
>       assert 0  # for demo purposes
E       assert 0

test_module.py:6: AssertionError
_______ test_noop ________

smtp = <smtplib.SMTP object at 0xdeadbeef>

    def test_noop(smtp):
        response, msg = smtp.noop()
        assert response == 250
>       assert 0  # for demo purposes
E       assert 0

test_module.py:11: AssertionError
======= 2 failed in 0.12 seconds ========
```

두개의 assert 0 실패가 보이고 더 중요한건 같은(모듈 범위인) smtp 객체가 두 테스트 함수로 전달된다는 것을 볼 수 있다는 것이다. 이는 pytest가 트랙백에 들어오는 인자값을 보여주기 때문이다. 그 결과, smtp를 사용하는 두개의 테스트 함수들은 같은 인스턴스를 재사용하기 때문에 하나의 함수가 작동되는 것만큼 빠르게 작동한다는 의미입니다.  

세션 범위의 smtp 인스턴스를 사용하려고 결정했다면, 다음과 같이 간단하게 선언할 수 있습니다:
``` python
@pytest.fixture(scope="session")
def smtp(...):
    # the returned fixture value will be shared for
    # all tests needing it
```

## fixture finalization / teardown 코드 실행

pytest는 fixture가 스코프를 벗어날 때 특정 finalization 코드를 실행하는 것을 지원합니다. return 대신 yield 상태를 사용해서, yield 상태 후에 모든 코드들은 teardown code로서 사용됩니다.

``` python
# content of conftest.py

import smtplib
import pytest

@pytest.fixture(scope="module")
def smtp(request):
    smtp = smtplib.SMTP("smtp.gmail.com")
    yield smtp  # provide the fixture value
    print("teardown smtp")
    smtp.close()
```

print와 smtp.close() 상태들은 모듈에서 가장 마지막 테스트가 종료 작업을 실행 했을 때 실행될 것입니다. 테스트들의 예외 상태는 고려되지 않습니다.  

실행해봅시다.
``` shell
$ pytest -s -q --tb=no
FFteardown smtp

2 failed in 0.12 seconds
```

smtp 인스턴스는 두 테스트들이 실행을 종료한 후에 finalize됩니다. fixture함수를 ```scope='function'```으로 decorate 했다면 fixture 설정과 cleanup은 각각의 단일 테스트 주변에서 일어날 것입니다. 어느 경우에나, 테스트 모듈은 fixture 설정의 이런 자세한 부분들을 알거나 바꿀 필요가 없습니다.  

with 상태에 yield syntax를 사용해서 끊어진 사이가 없게(seamlessly) 할 수 있습니다.  

smtp 연결은 테스트가 실행을 종료한 후에 닫혀질 것입니다. 이는 smtp 객체가 with 상태 종료일때 자동적으로 닫히기 때문입니다.
