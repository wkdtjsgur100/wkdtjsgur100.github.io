---
layout: post
section-type: post
title: Python Testing을 위한 python coverage
category: python
tags: [ 'test', 'python', 'python-coverage' ]
---

# python coverage

> python 코드의 테스팅이 잘 이루어지는지 확인하기 위한 python package

installation, Usage, Upgrade 등 자세한 내용은 [python 공식 홈페이지](https://pypi.python.org/pypi/pytest-cov)를 통해 확인 할 수 있고,

pytest-cov를 사용하게 되었을 때 볼 수 있게 되는 report의 예시는 다음 그림과 같다.

<img alt="pycov-exam" src = "/assets/pycov-exam.png"/>

Coverage와 관련하여 아래과 같은 기능을 지원해 주는 [codecov 사이트](https://codecov.io)를 이용해보자.<br>

<img alt="cov-feature" src = "/assets/codecov.png"/>

codecov 사이트는 20가지가 넘는 언어를 지원하고, Travisci, Circleci등 여러 CI를 지원하는 것을 알 수 있다.<br>
또 이 사이트에서 가장 매력적으로 보이는 기능은 CI + github와의 auto-merging 서비스이다.<br>
(github repository에 push하게 되면, circleci 빌드와 함께 테스팅이 몇퍼센트나 됐는지 나오게 된다.)<br>
아래는 auto-merging 서비스의 예시 화면.

<img alt="auto-merging" src = "/assets/auto_merging.png"/>
