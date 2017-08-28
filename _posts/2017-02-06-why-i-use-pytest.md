---
layout: post
section-type: post
title: 왜 pytest를 써야할까?
category: python
tags: [ 'python', 'test', 'pytest' ]
---

> 본 글은 Andrew J Todd esq.가 작성한 [이 링크](http://halfcooked.com/presentations/pyconau2013/why_I_use_pytest.html) 를 해석한 자료입니다.

# Intro

unit testing을 해본적이 있나요? 항상 프로젝트에 테스트를 추가하려 했지만 어디에서 시작할지 몰랐나요? 이 글은 당신의 모듈, 패키지 혹은 전체 프로젝트의 unit testing을 친절하게 설명합니다.  

unit testing의 이점을 설명하는 많은 글들, 책들, 블로그 포스트, 튜토리얼 비디오들은 정말 많이 있기에 여기서 이들과 관련된 자세한 내용들은 다시 설명하지 않습니다. 여기 페이지 맨 아래에 좋은 unit testing 튜토리얼과 관련한 몇몇 링크들을 첨부해두었습니다.  

이 글은 여러분이 최소 unit testing을 들어 보았고, 과거에 프로젝트를 위해 테스트를 작성하려는 시도를 해보았다고 가정합니다. 당신이 시작한 후에 멈췄거나 너가 이미 작성했었었던 코드들이 전부 잘 됐음에도 어렵다고 생각된다면, 이 글을 읽으세요.  

이 글을 위해 프로젝트는 몇몇 파이썬 코드로 구성됩니다(단일 기능에서 부터 완전한 app system 까지). 또한 전체 애플리케이션 시스템을 테스트하는 것과 같은 기능을 하는 테스트에도 동일한 원칙이 적용됩니다.

# Unit Testing이란 무엇인가?

우리는 명확하게 일단 몇몇 기초 개념을 빠르게 훑겠습니다. Unit Testing은 어떤 한 모듈 혹은 애플리케이션 내 코드가 예상대로 작동하는지 점검하는 반복가능한 어떤 활동이고, 이는 대개 당신의 코드를 테스트하는 코드의 형태로 구성됩니다. 우리는 주로 테스트 코드를 테스트 하는 코드를 작성할 필요는 없습니다. Unit Testing의 핵심적인 특징은 테스트들은 서로 분리되어 있어야 한다는 것, 실행은 자동화 되어야 한다는 것, 응용프로그램의 동일한 부분을 테스트하는 테스트가 일괄처리로 그룹화 된다는 것입니다.  

테스트들은 서로 테스트 되고 있는 코드와 서로 분리되어 있습니다. 이는 테스트 코드들이 더 쉽게 실패하는 문제를 찾고 고칠 수 있게 해줍니다.  

편의상 Unit Testing은 자동화 된 경우에만 작동합니다. 대개 애플리케이션 코드보다 더 많은 테스트 코드가 있으며 시간의 흐름에 따라 수작업으로 테스트를 진행하는 것은 좋은 방법이 아닙니다. 이상적으로 모든 테스트는 단일 명령으로부터 실행되어야 합니다. 실제로 이런 접근들은 편리한 기능을 통해 할지 테스트들을 발견하고 실행하는 모듈들에 의해 대부분의 테스트 라이브러리들의 접근법입니다.  

테스트는 필요한 경우 개발자들이 전체 테스트 케이스의 subset들을 실행 가능하게 하기 위해 일괄처리로 그룹화 되어있습니다. 이는 이미 존재하는 테스트들을 추가하고 바꾸는데 더 쉽게 만들어주게 됩니다.

# 왜 Unit Test 인가?

명확한 이유 외에 - "내 코드가 동작하는것을 아는것" - 프로젝트에 Unit Testing을 적용하는데 다른 이점들이 있습니다. <br>

Unit Testing은 너가 코드를 '어떻게' 작성하는지에 대해 생각 할 수 있도록 해줍니다. 또 코드가 무엇을 하고 있는지 코드들이 적절한지 알려주고 구현 선택에 결코 손해를 주지 않습니다. Unit test된 코드는 다르게 보일 것인데 주된 효과는 unit test들을 추가하는 것이 어플리케이션의 각각의 유닛(함수/기능)들을 더 작게 만들어 주는 것입니다. 이는 코드를 이해하고, 테스트하는데 쉽게 만들어 주게 합니다. 코드의 Unit을 더 작게 하는 것은 좋습니다. 아래의 설명은 StackOverFlow의 MichaelT이 훌륭히 설명해놓았습니다.<br>

    많은 것을 하는 테스팅 코드는 어렵다.

    많은 것을 하는 디버깅 코드는 어렵다. 이러한 문제를 해결할 수 있는 것은 많은 것들을 하지 않는 코드를 작성하는 것이다. 각각의 기능들을 딱 한가지만 하는 하나만 작성해라. 이는 유니테스트를 테스트하는데 좀 더 쉽게 만들어준다(하나의 유닛은 무수한 유니테스트를 필요로 하지 않는다)

    내 동료는 그가 주어진 기능들이 더 작게 쪼개질 필요가 있는지 판단하는 구문이 있다:

    만약, 다른 프로그래머에게 코드의 작동을 설명할때 '그리고' 라는 단어를 사용하게 된다면, 이 기능은 최소 하나 이상의 기능으로 분할되어야 합니다.

다른 Unit Testing의 이점은 문제를 빠르게 발견하고, 변화를 수월하게 하고, 통합을 간소화 시키며 디자인을 향상시키는데 있습니다. 자세한건 Wikipedia에 있는 [Unit Testing 글](https://en.wikipedia.org/wiki/Unit_testing) 을 참조하세요.

# Unit Test 라이브러리

파이썬 표준 라이브러리는 [unittest](https://docs.python.org/2/library/unittest.html) 입니다. 이는 Java의 [junit](http://junit.org/junit4/)을 기반으로 합니다. 만약 자바 프로그래머 라면 unittest는 시작점이 될 것입니다.<br>

누군가를 위한 다른 훌륭한 대안책은 [py.test](http://docs.pytest.org/en/latest/) 입니다. py.test로 프로젝트 테스트를 시작하는 것은 매우 쉬운데, 이는 테스트들에 필요한 매우 작은 [boiler plate](http://www.terms.co.kr/boilerplate.htm) 들이 있기 때문입니다. py.test로 테스팅을 시작하는 것은 빠르게 할수는 있겠지만 기본적인 라이브러리와는 거리가 멉니다. 이는 필요한 도구 및 테스트 기능들이 모두 있습니다. 트랙백 보고서는 뛰어나면서도 매우 효과적인 테스트 컬렉션과 명령들, 테스트 건너뛰기, 매개변수화된 테스트들을 작성하는 기능들을 제공합니다. 좀 더 자세한 설명은 [py.test](http://docs.pytest.org/en/latest/) 웹사이트를 통해 확인 하실 수 있습니다.<br>

py.test를 계속해서 사용하고 선택한 이유는 간단한 테스트 컬렉션이 있고, boilerplate의 부족, 테스트 시 클래스 또는 모듈 수준에서 설정 및 제거 기능이 있기 때문입니다. 예를 들어 이러한 함수가 있다고 하면: <br>

``` python
def parse_connection(connection_string):
      """Work out the user name and password in connection_string

      Connection string should be of the form 'database://username@password'
      """
      if '@' in connection_string:
          # Username is characters between '://' and '@'
          slash_position = connection_string.find('://')
          at_position = connection_string.find('@')
          user_name = connection_string[slash_position+3:at_position]
          password = connection_string[at_position+1:]
          return user_name, password
```
여기 unittest를 작성할 수 있는 가능한 가장 간단한 테스트가 있습니다.

``` python
    from parse_conn import parse_connection
    import unittest

    class InvalidInputs(unittest.TestCase):
        def testNoAt(self):
            """parse_connection should raise an exception for an invalid connection string"""
            self.assertRaises(ValueError, parse_connection, 'invalid uri')

    if __name__ == "__main__":
        unittest.main()
```
반면에 py.test는 같은 테스트를 좀 더 간단하게 할 수 있습니다:

``` python
    from parse_conn import parse_connection
    import py.test

    def test_not_at():
        py.test.raises(ValueError, parse_connection, 'invalid uri')
```
unittest.TestCase가 무엇이 있는지, 테스트를 돌리는데 unittest.main을 발생시킬 필요가 있는지에 대해 알 필요가 없습니다. command line에서 옵션 없이 py.test를 실행하면 현재 디렉토리에서 test로 시작하는 파일 이름을 찾고 이름이 test로 시작하는 파일에서 함수 또는 메소드를 실행합니다. 물론 특정 모듈과 테스트들에 대해서 이 기능들 외에도 좀더 구체화 시킬 수 있겠지만, 이 기능은 py.test가 unittest를 이깁니다.<br>

본질적으로 저는 unittest 보다 py.test를 더 선호합니다. 그 이유는 제가 저의 코드를 테스팅하기 시작할 때 일을 더 적게 하기 위해서 나에게 필요한 것이기 때문입니다. [unittest2](https://pypi.python.org/pypi/unittest2) 에서는 py.test의 편리성이 표준 라이브러리로 적용된 것입니다.<br>

제가 py.test를 좋아하는 또 다른 이유는 클래스 또는 모듈 수준에서 테스트의 기능들을 해제 설정할 수 있다는 것에 있습니다(일관된 테스트 상태를 유지하기 위해). 이는 사실 하나 이상의 테스트를 같이 설정하고 잠재적으로 다른 테스트에 영향을 미치기 때문에 분리 원칙을 위반하는 것입니다. 하지만 이건 적절하게 사용하는 실질적인 좋은 예제들은 Zen of Python의 순수 관점을 능가합니다. 복잡하거나 비싸고 큰 설정을 요구 하는 테스트 기능들을 필요로 하는 곳에 - 전체 데이터베이스를 만드는 것이나 거대한 랜덤화된 테스트 데이터 셋 같은 -  각각의 테스트 셋팅을 반복하는 것은 매우 큰 시간이 듭니다. 긴 테스트들을 작동시키는 것은 자주 쓰이지 않습니다. 만약 다른 테스트들과 방해하는 것 없이 테스트 기능들 사이에 상태를 공유할 수 있다면 복잡한 설정과 해제 기능을 공유하는 것은 전체 테스트 케이스를 작동 시키는 데 많은 시간을 줄여줍니다.

# 어떻게 Unit Testing을 시작 할 것인가

더 중요한 것은 Unit Testing을 어떻게 시작할까요? 많은 사람들은 애플리케이션, 파일의 컬렉션, 단지 심플한 하나의 모듈을 테스팅 하려고 애를 쓰는 상황에 놓입니다. 속담에 이런 말이 있는데 - 천리길도 한걸음 부터 - 그렇다면 우리는 어떻게 한걸음을 내딛어야 할까요?<br>

저는 과거에 어플리케이션의 전체 테스트 모듈을 만드는 것은 너무 많은 일을 해야한다고 생각했고 '나중의' 일로 미뤘었습니다. 모든 테스트들을 작성하는데 수 일이 걸렸었고 이는 작업환경의 프로젝트 매니저에게 정당화 될 수 없는 일이었습니다. 개인 프로젝트일 경우 더 많은 기능들을 추가할 수 있을 때 왜 테스트를 만드는 데 시간을 들여야 할까. 구글에서 'hot to start unit testing'을 검색하면 113,000,000개의 결과가 반환된다. 이는 꽤 대중적인 질문이지만 이에 대해 상충되는 의견이 많이 있음을 말해줍니다.<br>

제가 발견했었던 애플리케이션 Unit Testing을 시작하는 가장 쉬운 방법은 버그를 고치는 것으로서 할 수 있습니다. 방법은 다음과 같습니다.

* 버그를 찾는다.
* 버그가 고쳐졌을 때 통과할만한 테스트를 작성한다.
* 테스트가 통과 될 때까지 코드를 바꾼다.

여기서 핵심은 단위 테스트들을 추가해서 이득을 얻기 위해 코드를 100% 커버 할 필요가 없다는 것 입니다. 단일 테스트는 없는 것보다는 낫고, 많은 테스트 케이스들은 하나만 있는 것보다는 낫습니다. 여러분의 코드가 내 코드랑 비슷하고 많은 버그를 포함하며 이러한 접근을 따라갔을때 포괄적인 테스트들을 매우 신속하게 구축할 수 있습니다.<br>

애플리케이션에 기능을 추가 할때 다음과 같이 접근하세요

* 새로운 기능들이 작동할 때 통과할 테스트를 작성한다.
* 테스트가 패스 될 때까지 코드를 바꾼다.

이는 대개 버그를 고치는 접근 방식과 거의 비슷한 목적이다. 어플리케이션에 테스트를 넣는 것에 대해 내가 마지막으로 권장하는 것은 몇몇 코드를 보수할 때 이러한 일들을 하는 것이다. 내가 최근 본 이 예제를 살펴보자:

``` python
class Portfolio(object):
        ...
        def get_portfolio(self, portfolio_code):
            ...
            stmt = """SELECT id FROM portfolios WHERE code=?"""
            cursor.execute(stmt, (portfolio_code,))
            portfolio_id = cursor.fetchone()[0]
            ...
        def add_value(self, portfolio_code, ...):
            ...
            stmt = """SELECT id FROM portfolios WHERE code=?"""
            cursor.execute(stmt, (portfolio_code,))
            portfolio_id = cursor.fetchone()[0]
            ...
```
저는 몇가지 방법으로 몇몇 코드를 반복하는 작업을 했었었습니다. 그래서 반복된 라인들을 다른 메서드로 나누었고 몇몇 테스트들을 작성했습니다. 여기엔 제가 일찍이 언급했었던 두가지 이점이 있는데 제 코드를 이해하기 쉽게 만들어 주고 각각의 기능들이 한가지만 명확히 하도록 해주는 것입니다. 또 저는 제 어플리케이션에 필요한 unit test들을 충분히 추가했습니다.<br>

이러한 Unit Testing에 대한 접근은 조급한 최적화를 피할 수 있습니다. 만약 모든 가능한 테스트를 작성하고 시도하는 것은, 마치 애플리케이션에 모든 가능한 기능들을 추가하는 것처럼 당신은 필요없는 코드와 필요없는 기능으로 끝나는 경우가 많습니다.<br>

제가 하고싶은 말은 현재 변화하고 있는 코드에 대해서만 코드를 작성하라는 것입니다. '분석 마비'를 야기하는 모든 가능한 테스트 시나리오를 고려하는 것은 좋은 생각이 아니다. 당신은 실제로 유용한 테스트들을 작성하는데 테스트되는 순열과 조합에 관해서 너무 많이 생각하고 있다. 또한 드물게 실행되는 애플리케이션의 일부분을 위해 테스트를 작성하는데 시간을 너무 낭비하지 않아도 됩니다.<br>

코드를 위한 자동화된 unit 테스트들을 작성할 때 몇몇 주목해야 할 다른 점들이 있다. Unit 테스트 코드는 다르게 보일것입니다. 여기엔 좀더 여러 기능들과 방법들이 있을것이고 이들은 더 작아질 것입니다. Unit Testing은 당신이 각각의 메서드가 한가지만 훌륭히 수행하는데 집중하게 해줍니다.

# 내 테스트 코드는 어디에 있어야 할까?

py.test를 사용했을 때 테스트 코드를 넣어야 할 곳은 어디일까? 유일하게 어려우면서도 빠른 방법은 어플리케이션 코드로 부터 분리하고 유지하는 것이다. 같은 파일에 테스트 코드와 애플리케이션 코드를 넣을 수도 있지만, 이것은 나중엔 코드의 베이스를 더 어렵게 만들게 된다. 좋은 방법은 테스를 함께 그룹짓고 명확히 구분시키는 것이다. py.test는 test가 포함된 메서드나 클래스 이름으로 시작하는 것을 권장하기 때문에 여기서 도움이 됩니다. 세트를 완성하기 위해 저는 대체로 tests로 불리는 어플리케이션 코드와 같은 디렉토리에 뒀습니다.<br>

사람들이 Unit Testing에 대해서 생각하는 또다른 문제는 사람들이 애플리케이션을 바꿨을때 두가지코드(어플리케이션과 코드)를 둘다 바꿔야 한다는 생각입니다. 이는 사실임에도 불구하고 깨지기 쉬운 삶의 진리입니다. 애플리케이션 기능이 테스트를 바꾼다면 테스트도 마찬가지로 바꿀 필요가 있는 것입니다. 위에서 보여줬던 것중 하나가 가장 좋은 방식입니다. 응용 프로그램의 영역에서 테스트를 수정하려는 경우 먼저 테스트를 변경하면 됩니다. 그리고 모든 테스트들이 다시 패스 될때 까지 코드를 업데이트하면 될 것입니다.<br>

마지막으로, 저는 Pod Delusion podcast의 이슈 182 를 듣고 있었고 이는 USS Thresher 잠수함의 잃어버린 한 부분입니다. 이 잔해는 당신에게 남기고 싶었던 훌륭한 문구가 있었습니다. - "결과를 듣고 싶지 않다면 테스트를 실행하지 마라"

# 읽을거리

단위 테스팅에 대한 몇가지 소개(주로 unittest 사용)

http://pythontesting.net/framework/pytest-introduction/
http://www.onlamp.com/pub/a/python/2004/12/02/tdd_pyunit.html

Testing tools and libraries

http://wiki.python.org/moin/PythonTestingToolsTaxonomy
py.test - http://pytest.org
nose - https://nose.readthedocs.org/en/latest
testfixtures - http://www.simplistix.co.uk/software/python/testfixtures - by Chris Withers from Simplistix
testtools - by Robert Collins - https://pypi.python.org/pypi/testtools
subunit - by Robert Collins - https://launchpad.net/subunit
Zope has it's own test runner - https://pypi.python.org/pypi/zope.testrunner
Twisted 'trial' - https://twistedmatrix.com/trac/wiki/TwistedTrial
PyFIT - http://pyfit.sourceforge.net/
Coverage.py - http://nedbatchelder.com/code/coverage/
testtools - https://github.com/testing-cabal/testtools
ATOMac - GUI testing of Mac applications - http://github.com/pyatom/pyatom
tox - http://testrun.org/tox/latest/ - Virtualenv package manager for testing installation, use of multiple Python versions and Continuous Integration. Aimed at enabling you to test you code against multiple interpreter and dependency configs
behave - https://pypi.python.org/pypi/behave
