---
layout: post
section-type: post
title: pytest를 사용해보자 (2)
category: pytest
tags: [ 'pytest', 'tdd','python','test' ]
---

# Setting a breakpoint / aka set_trace()

breakpoint를 설정하고 ```pdb.set_trace()```를 입력하려면 helper를 사용할 수 있습니다.

``` python
import pytest
def test_function():
  ...
  pytest.set_trace()  # invoke PDB debugger and tracing
```

pytest 버전 2.0.0 이전에 ```pytest -s``` 명령어로 캡처하는 것을 비활성화 시키면 PDB tracing을 입력 할 수 있었지만, 그 이후의 버전은 pytest가 자동적으로 비활성화 시켜준다.

* 다른 테스트들의 출력 캡처는 영향을 미치지 않는다.
* 이미 캡쳐 된 이전 테스트 출력은 그대로 처리됩니다.
* 동일한 테스트 내 에서 생성된 이후의 출력은 캡처되지 않고 직접적으로 ```sys.stdout```에 보내집니다. 대화형 PDB 추적 세션을 종료하고 정기적인 테스트 실행을 계속 한 후 테스트 출력 발생 조차도 true가 유지된다는 점에 주목해야 합니다.

pytest 버전 2.4.0부터 pytest.set_trace() wrapper를 사용하거나 pytest -s를 통해 pytest의 출력 캡쳐를 명시적으로 비활성화 할 필요없이 기본 Python import pdb; pdb.set_trace() 호출을 사용하여 PDB 추적을 시작 할 수도 있습니다.

# 테스트 실행 지속성 프로파일링

가장 느린 10개의 테스트 지속성들의 리스트를 얻기 위한 명령어

``` text
pytest --durations=10
```

# JUnitXML 형식 파일 생성

Jenkins나 다른 CI(Continuous integration) 서버들에게 read 될 수 있는 파일들을 생성하기 위해서, 이 명령어를 사용한다.

``` text
pytest -- junitxml=path
```

이는 path에 XML file을 생성한다.

## record_xml_property

 버전 2.8에서 새로 추가

테스트에 추가적인 정보 log를 원한다면, recod_xml_property를 사용할 수 있다:

``` python
def test_function(record_xml_property):
    record_xml_property("example_key", 1)
    assert 0
```

이 코드는 생성된 testcase tag에 추가 속성 example_key="1"을 추가 할 것이다.

``` xml
<testcase classname="test_function" file="test_function.py" line="0" name="test_function" time="0.0009">
  <properties>
    <property name="example_key" value="1" />
  </properties>
</testcase>
```
> 주의: 이것은 경험에 의한 특성이고, 이 인터페이스는 나중에 새로 나올 버전에 좀 더 일반적이고 강력한 뭔가로 대체 될 수 있지만, 기능은 유지될 것입니다.
>
> 지금은 pytest-xdist 플러그인과 함께 사용하면 작동하지 않습니다.
>
> 또 이 기능을 하용하는 것은 어떤 스키마 인증서를 파괴한다는 것을 인지하십시오. 이는 어떤 CI 서버들과 함께 사용하였을 때 문제가 일어날 수 있습니다.

# LogXML : add_global_property

 버전 3.0에서 새로 추가

모든 테스트 케이스와 관련있는 속성들을 포함한 testsuite 레벨에서 속성 노드를 추가 하려면, ```LogXML.add_global_properties```를 사용할 수 있다.

``` python
import pytest

@pytest.fixture(scope="session")
def log_global_env_facts(f):

    if pytest.config.pluginmanager.hasplugin('junitxml'):
        my_junit = getattr(pytest.config, '_xml', None)

    my_junit.add_global_property('ARCH', 'PPC')
    my_junit.add_global_property('STORAGE_TYPE', 'CEPH')

@pytest.mark.usefixtures(log_global_env_facts)
def start_and_prepare_env():
    pass

class TestMe:
    def test_foo(self):
        assert True
```

이는
