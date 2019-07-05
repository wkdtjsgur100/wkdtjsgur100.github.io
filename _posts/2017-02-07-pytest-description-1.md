---
layout: post
section-type: post
title: pytest를 사용해보자 (1)
category: pytest
tags: [ 'pytest', 'tdd','python','test' ]
---

> pytest [공식 문서](http://doc.pytest.org/en/latest/contents.html)를 바탕으로 요약 정리 한 문서

# 설치

``` text
pip install -U pytest
```

# 예제

``` python
# content of test_sample.py
def func(x):
    return x + 1

def test_answer():
    assert func(3) == 5
```

# 실행

test_sample.py 파일이 있는 경로에서 pytest 입력  
아래는 실행 결과이다.  
![실행 결과](/images/posts/pytest1.png)

pytest에서 test 파일을 감지하는 방법은 [pytest convention rule](http://doc.pytest.org/en/latest/goodpractices.html#test-discovery)에 나와있는데 간단히 요약하면 다음과 같다.

* 기본적으로 arg가 없으면 현재 디렉터리에 있는 test파일을 찾지만, 옵션으로 줄 수도 있다.
* recursive dir가 아니면([norecursedirs](http://doc.pytest.org/en/latest/customize.html#confval-norecursedirs)) 재귀적으로 디렉터리를 탐색한다.
* 디렉터리를 탐색할 때 ``` test_*.py ``` 또는 ``` *_test.py ``` 를 찾는다.
* 위에서 찾은 파일을 바탕으로, 아래와 같은 test 요소들을 수집한다.
  * 이름이 test_ 로 시작하는 클래스 외부의 function.
  * 클래스 이름이 Test로 시작하는 클래스 내에서 이름이 test_로 시작하는 메서드(``` __init__ ```이 없어야 함)
* 파이썬 모듈에서는, 표준 [unittest.TestCase](http://doc.pytest.org/en/latest/unittest.html#unittest-testcase) 을 사용해 테스트들을 감지한다.

# 다중 테스트 실행

## 특정 exception이 일어나는 것을 assert 해보자

``` python
# content of test_sysexit.py
import pytest
def f():
    raise SystemExit(1)

def test_mytest():
    with pytest.raises(SystemExit):
        f()
```

[with 구문](http://ingorae.tistory.com/505)을 사용해서 예외를 raises를 시킨 코드이다.
위의 프로그램을 -q 옵션(로그를 간단히 표시하는 옵션)을 통해 실행해 보면, 아래와 같다

```
$ pytest -q test_sysexit.py
.
1 passed in 0.12 seconds
```

# Going functional: requesting a unique temporary directory

Functional 테스트의 경우 몇몇 파일을 만들어서 어플리케이션에 넘겨야 할 수 있습니다. 인자를 넘기면 pytest는 fixture factory를 찾아서 테스트 함수 호출을 진행하기 전에 resource 제작을 하도록 합니다.

예를 들어 아래와 같은 파이썬 파일을 만든다하면

``` python
# content of test_tmpdir.py
def test_needsfiles(tmpdir):
    print (tmpdir)
    assert 0
```

자세한 정보는 [Temporary directories and files](http://doc.pytest.org/en/latest/tmpdir.html#tmpdir-handling)
에 나와 있습니다.

pytest --fixtures를 치면, [pytest의
fixture들](http://doc.pytest.org/en/latest/fixture.html#fixtures) 을 확인 할 수 있습니다


# Usage and Invocations
``` text
python -m pytest [...]
```
을 이용해서 pytest를 사용할 수도 있는데, 이는 pytest [...] 랑 거의 비슷한 명령어입니다.

## version, option 이름, 환경 변수 도움말 사용 방법
```
pytest --version   # pytest 버전 확인
pytest --fixtures  # 사용 가능한 builtin function arguments 확인
pytest -h | --help # 커맨드 라인 및 설정 파일 옵션 확인
```

## N번째 실패시 테스팅 정지시키는 방법

```
pytest -x            # 첫 번째 실패 시 중지
pytest --maxfail=2    # 두 번째 실패 시 중지
```

## 테스트 선택 / 지정

``` text
pytest test_mod.py   # 모듈 내 테스트 실행
pytest somepath      # 특정 경로 아래 모든 테스트 수행
pytest -k stringexpr # 특정 "문자열 표현"과 이름이 매칭되는 테스트만 실행  
                      # 예시 : "My Class and not method"  
                      # TestMyClass.test_something은 선택 되지만  
                      # TestMyClass.test_method_simple을 선택 안됨
pytest test_mod.py::test_func  # "노드 id"와 매칭되는 테스트들만 실행
                                # 예시 : "test_mod.py::test_func"은
                                # test_mod.py의 test_func만 실행된다.
pytest test_mod.py::TestClass::test_method  # 단일 클래스 내 단일 메서드 실행

pytest --pyargs pkg # pkg 디렉터리 하위에 있는 모든 테스트를 실행합니다.
```

## Modifying Python [traceback](https://ko.wikipedia.org/wiki/%ED%8A%B8%EB%9E%99%EB%B0%B1) printing

```
pytest --showlocals # trackback 내 모든 지역 변수들을 보여준다.
pytest -l           # 모든 지역 변수들을 보여준다(short cut)

pytest --tb=auto    # (기본 설정) 첫 번째와 마지막 엔트리는 '긴' trackback 이지만
                    # 다른 엔트리는 '짧은' trackback
pytest --tb=long    # 완전한 정보들을 제공하는 형식의 trackback
pytest --tb=short   # 짧은 형식의 trackback
pytest --tb=line    # 실패 당 한 줄로 표시
pytest --tb=native  # 파이썬 표준 포맷
pytest --tb=no      # trackback 없음
```
--full-trace 옵션도 있긴한데 넘길어서 에러가 일어난다 한다. 그래서 Ctrl+C로 도중에 중단 시킬수 있다고 한다.

## Dropping to [PDB(Python Debugger)](https://docs.python.org/3/library/pdb.html) on failures

pytest는 아래와 같이 커맨드 라인 옵션을 통해 PDB 에 드롭할 수 있습니다.
``` text
pytest --pdb
```
이는 모든 실패 시 마다 파이썬 debugger를 호출합니다. 종종 특정 실패 시 에만 호출하길 원할 경우엔 아래와 같이 한다.
``` text
pytest -x --pdb   # 첫번째 실패를 PDB에 드롭하고, 테스트 세션을 종료한다.
pytest --pdb --maxfail=3  # 처음 세 번의 실패를 PDB에 드롭한다.
```
모든 실패 시엔 예외정보는 sys.last_value, sys.last_type, sys.last_traceback에 저장됩니다. interactive로 사용하면 디버그 도구로 사후 디버깅을 할 수 있습니다. 이것은 또한 예외 정보에 수동적으로 접근 할 수 있습니다.
``` text
>>> import sys
>>> sys.last_traceback.tb_lineno
42
>>> sys.last_value
AssertionError('assert result == "ok"',)
```
