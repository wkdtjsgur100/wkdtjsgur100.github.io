---
layout: post
section-type: post
title: Ubuntu에서 pyenv, virtualenv, autoenv 설정 방법
category: python
tags: [ 'python' ]
---

# pyenv, virtualenv, autoenv ??

## pyenv란?

로컬에서 다양한 파이썬 버전을 쉽게 쓸 수 있도록 도와주는 것.  
어떤 프로젝트에서는 python 3.5.2를, 다른 프로젝트에서는 python 2.7.3을 쓰고 싶거나 할 때 쓰인다.  

## virtualenv란?

로컬에서 다양한 파이썬 환경을 구축하기 위한 것.  
python package(pip)에 관한 의존성을 해결해 줄 수 있다.  

## autoenv란?

virtualenv를 설정해주고 나면 프로젝트 폴더를 들어갈때마다 매번 pyenv activate 명령어를 이용해서 virtualenv을 활성화 시켜줘야 하는데, 그것을 자동으로 해주는 것이다.


# 설정 가이드

## pyenv 설정

pyenv를 설치해서 python 버전을 관리하자.(버전은 python 3.6.0으로 설치하겠다)

``` shell
# dependency 설치
sudo apt-get update
sudo apt-get install -y make build-essential libssl-dev zlib1g-dev libbz2-dev \
libreadline-dev libsqlite3-dev wget curl llvm libncurses5-dev libncursesw5-dev \
xz-utils tk-dev

# pyenv git clone
git clone https://github.com/pyenv/pyenv.git ~/.pyenv
echo 'export PYENV_ROOT="$HOME/.pyenv"' >> ~/.bash_profile
echo 'export PATH="$PYENV_ROOT/bin:$PATH"' >> ~/.bash_profile
echo 'eval "$(pyenv init -)"' >> ~/.bash_profile

source ~/.bash_profile
# python version 3.6.0 설치
# 설치 가능한 버전은 pyenv install --list 명령어로 확인할 수 있다.
pyenv install 3.6.0
```

pyenv 3.6.0을 설치하는데 시간이 좀 오래걸린다. 여유를 가지고 기다리도록 하자.  
설치가 완료되면, 다음과 같은 명령어로 설치된 pyenv version을 확인할 수 있다.

``` shell
$ pyenv versions
  3.6.0
```

## virtualenv 설정

다음과 같은 명령어로 virtual-env의 환경설정을 해주자. virtualenv의 env명은 임의로 `test-3.6.0-env`로 설정했다.

``` shell
git clone https://github.com/yyuu/pyenv-virtualenv.git ~/.pyenv/plugins/pyenv-virtualenv
echo 'eval "$(pyenv virtualenv-init -)"' >> ~/.bash_profile
source ~/.bash_profile
pyenv virtualenv 3.6.0 test-3.6.0-env
```

생성된 virtualenv를 마찬가지로 `pyenv versions` 명령어를 이용해 다음과 같이 확인 할 수 있다.

``` shell
~$ pyenv versions
  3.6.0
  3.6.0/envs/test-3.6.0-env
  test-3.6.0-env
```

그리고 다음 명령어를 사용해서, virtualenv를 활성화 시킬 수 있다.

``` shell
pyenv activate test-3.6.0-env
```

활성화가 되면, 다음과 같이 쉘 왼쪽에 (test-3.6.0-env)가 표시된다.

``` shell
(test-3.6.0-env) jang:~/testdir$
```


## autoenv 설정

다음과 같은 명령어로 autoenv를 설정한다.(virtualenv의 이름을 주의하자.)

``` shell
git clone git://github.com/kennethreitz/autoenv.git ~/.autoenv
echo 'source ~/.autoenv/activate.sh' >> ~/.bash_profile
source ~/.bash_profile

```

그리고 virtualenv를 적용시킬 폴더에서 다음과 같은 파일을 만든다.

``` shell
touch .env
echo "pyenv activate test-3.6.0-env" > .env
```

해당 프로젝트로 이동하고 나면, 자동으로 `test-3.6.0-env`가 활성화된다.  
