---
layout: post
section-type: post
title: Ubuntu에서 selenium 시작하기
category: selenium
tags: [ 'selenium' ]
---

# environment

## Backend

- Selenium 3.8.0
- Ubuntu 16.04
- Firefox
- Python 3.6.0
- Nginx
- UWSGI

## Frontend

- React
- create-react-app

## AWS 설정

먼저 AWS에서 인스턴스를 생성하자  
인스턴스는 생성할 때 운영체제로 ubuntu를 선택해야 한다.  
세부 선택 사항에서, 용량은 30GB로 늘려야 여유가 있다.  

인스턴스가 생성되고 나면, HTTP 접속을 위해 80번 포트를 열어줘야 한다.  
인스턴스 리스트 하단에 보안 그룹 - launch-wizard-2를 클릭하고, 보안 그룹 페이지가 나오면  
하단의 인바운드 - 편집 버튼을 눌러서 유형 HTTP를 '추가'하자.  

인스턴스 셋팅이 끝났으면 shell에서(Mac인 경우엔 iterm이나 bash shell, Window인 경우엔 putty를 사용하시면 됩니다.)

``` shell
sudo ssh -i [Instance Key Path] ubuntu@[IP Address]
```

명령어를 이용해 AWS에 접속하자.  

접속 한 후 한국어 패키지를 사용하도록 하자.(python 파일이나 로그에서 한국어 인식할 수 있도록)

``` shell
sudo apt-get install language-pack-ko
```

## pyenv 설정

pyenv를 설치해서 python 버전을 관리하자.(버전은 python 3.6.0으로 설치하겠다)

``` shell
sudo apt-get update
sudo apt-get install -y make build-essential libssl-dev zlib1g-dev libbz2-dev \
libreadline-dev libsqlite3-dev wget curl llvm libncurses5-dev libncursesw5-dev \
xz-utils tk-dev

git clone https://github.com/pyenv/pyenv.git ~/.pyenv
echo 'export PYENV_ROOT="$HOME/.pyenv"' >> ~/.bash_profile
echo 'export PATH="$PYENV_ROOT/bin:$PATH"' >> ~/.bash_profile
echo 'eval "$(pyenv init -)"' >> ~/.bash_profile

source ~/.bash_profile
pyenv install 3.6.0
```

다음과 같은 명령어로 설치된 pyenv version을 확인할 수 있다.

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

## git 설정

git clone [repository url]

## pip 설치

사용할 pip들을 설치하자. 먼저, `requirements.txt`를 아래와 같이 써준다.

``` txt
Django==1.11.7
django-cors-headers==2.1.0
djangorestframework==3.7.1
EasyProcess==0.2.3
pytz==2017.3
PyVirtualDisplay==0.2.1
selenium==3.7.0
```

django-cors-headers는 CORS 방지를 위해 설치하는 것이고, PyVirtualDisplay는 headless browser를 사용하기 위함이다.  

이제 requirements.txt를 이용해 파이썬 패키지들을 설치하자.

``` shell
pip3 install -r requirements.txt
```

## Django Rest Framework(DRF) 설정

`python manage.py migrate`를 이용해서 migrate를 해준 다음,  
`python manage.py runserver`를 이용해 제대로 작동하는지 테스트한다.

CORS를 해결하기 위해,  
django_project/setting.py를 열어서

``` python
ALLOWED_HOSTS = ['its.your.ip.address']

CORS_ORIGIN_WHITELIST = (
    '13.124.235.21',
    'its.your.ip.address',
)
```
를 추가하자.

## Nginx 설정

nginx를 설치하자.

``` shell
sudo apt-get update
sudo apt-get install nginx
```

http://[AWS_INSTANCE_IP_ADDRESS] 를 치면, 다음과 같은 문구가 보일 것이다.

``` txt
Welcome to nginx!

If you see this page, the nginx web server is successfully installed and working. Further configuration is required.

For online documentation and support please refer to nginx.org.
Commercial support is available at nginx.com.

Thank you for using nginx.
```

그리고 다음과 같은 명령어를 치면, nginx의 설정파일들을 볼 수 있다.

``` shell
sudo vim /etc/nginx/sites-enabled/default
```

nginx가 index.html 파일만 인식할 수 있도록 server 블록을 수정하자.

```
server {
    . . .
    index index.html; # index.nginx-debian.html, index.htm 삭제!
    . . .
}
```

## React 설정


먼저 노드와 npm을 깔고,
``` shell
sudo npm cache clean -f # 강제로 캐시 삭제
sudo npm install -g n # n 모듈 설치
sudo n stable # node stable 버전 설치
sudo npm install -g npm # npm 최신버전으로 업데이트
```

노드와 npm의 버전을 확인해보면, 정상적으로 설치되었다.
``` shell
$ sudo node -v
v9.2.0
$ sudo npm -v
5.5.1
```

package.json가 있는 폴더에 들어가서, 이제 npm을 깔자.

``` shell
sudo npm install
```
을 해주자.

``` shell
npm start
```
를 해보면, 정상적으로 동작하는 것을 확인할 수 있다.  

이제 shell 파일을 만들어서 react product를 deploy하자.

``` shell
#!/bin/bash

cd ~/inscheck/frontend/inscheck/;
sudo npm run build;
cd build/;
sudo cp index.html /var/www/html/;
sudo cp -a static/ /var/www/html/;
cd ~
sudo service nginx restart;

# set_up_frontend.sh
```

그리고 다음과 같은 명령어를 이용해 set_up_frontend의 권한을 바꿔주자.

``` shell
chmod +x 755 ./set_up_frontend
```

`./set_up_frontend`를 실행하게 되면,

## UWSGI 설정

``` shell
pip3 install uwsgi
```

``` shell
uwsgi --http :8000 --module [DJANGO_PROJECT_NAME].wsgi
```

# AWS 포트 열기

8000 포트를 임시로 열어두자.

# Firefox 설치

``` shell
sudo apt install firefox
```

이제 selenium 코드에서,

``` python
driver = webdriver.Firefox()
```
를 하게 되면,

``` shell
. . .   

selenium.common.exceptions.WebDriverException: Message: 'geckodriver' executable needs to be in PATH.
```

이라는 Exception이 뜨게 된다.  

[이 링크](https://github.com/mozilla/geckodriver/releases)를 들어가서 최신 버전을 확인하고 wget으로 geckodriver를 설치 후 /usr/local/bin에 위치시켜주자.

``` shell
cd /usr/local/bin  
sudo wget https://github.com/mozilla/geckodriver/releases/download/v0.19.1/geckodriver-v0.19.1-linux64.tar.gz  
sudo tar -xvf geckodriver-v0.19.1-linux64.tar.gz  
sudo rm -rf geckodriver-v0.19.1-linux64.tar.gz
```

xvfb를 설치하자

``` shell
sudo apt-get install xvfb
Xvfb :99 -ac & export DISPLAY=:99
```
