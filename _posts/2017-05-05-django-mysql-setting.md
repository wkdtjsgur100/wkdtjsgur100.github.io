---
layout: post
section-type: post
title: django에서 mysql을 사용해보자. (ubuntu 14.04, mac)
category: django
tags: [ 'django', 'mysql' ]
---

> django는 기본적으로 sqlite를 DB로 사용하고 있다. 하지만 서버의 확장성과 직접 DB를 다룰 때 유용한 mysql로 바꿀 때 어떻게 해야 하는 지를 작성했다.

## django 프로젝트에서의 설정.

프로젝트 setting.py 파일에

``` python
DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.sqlite3',
        'NAME': os.path.join(BASE_DIR, 'db.sqlite3'),
    }
}
```

다음과 같은 코드 조각이 있는데, 이 코드를 다음과 같이 바꿔준다

``` python
DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.mysql',
        'NAME': '프로젝트명',
        'USER': '사용자 이름',
        'PASSWORD': '비밀번호',
        'HOST': 'localhost',
        'PORT': '',
    }
}
```

여기서 NAME, USER, PASSWORD는 본인이 원하는 대로 값을 설정 할 수 있다.  
그리고 일단 먼저 django에서 mysql을 사용하기 위한 python package를 설치합니다.  

``` shell
pip install mysqlclient
```

이제 django에서 mysql을 사용하도록 설정했으니, DB를 현재 환경에 맞게 설치해보자.

### mac에서의 mysql 설치

#### 첫 번째 방법

``` shell
brew install mysql
```

homebrew를 사용해 설치했는데, yarn을 사용해서도 설치할 수 있다고 한다.  
그리고 나서 mysql 서버를 시작하기 위해서 다음 명령어를 사용해 서버를 시작한다.  

``` shell
mysql.server start
```

이렇게 했더니 재부팅 시 여러 에러가 추가적으로 발생해서, 다른 방법으로 mysql을 설치해 보았다.

#### 두 번째 방법

1. [mysql mac용 설치 URL](https://dev.mysql.com/downloads/mysql/)에서 mysql을 다운 받는다.
2. 설치하다 보면 임시 비밀번호가 발급 되는데, 잘 저장해 둔다.
3. 다음의 명령어를 사용해서 mysql에 접속한다.
``` shell
mysql -u root -p
```
패스워드는 임시 비밀번호를 입력한다.

4. 다음 명령어를 사용해 패스워드를 바꿔준다.
``` shell
SET PASSWORD FOR 'root'@'localhost' = PASSWORD('변경 비밀번호');
FLUSH PRIVILEGES;
```
5. DB에 접근하는 언어가 한국어면 에러를 내뿜게 되기 때문에, 인코딩 설정을 바꿔줄 것이다. 다음 파일을 편집해서 설정을 바꿔주자

``` shell
sudo vim /etc/my.cnf
```

다음은 /etc/my.cnf에 들어갈 내용이다.

``` text
[mysqld]
character-set-server=utf8
collation-server=utf8_general_ci
init_connect=SET collation_connection=utf8_general_ci
init_connect=SET NAMES utf8

[client]
default-character-set=utf8

[mysql]
default-character-set=utf8
```


### ubuntu 14.04 에서의 mysql 설치

``` shell
sudo apt-get update
sudo apt-get install mysql-server libmysqlclient-dev
```

### mysql user 추가하기

mysql DB에 접근 할 수 있는 user를 추가하기 위해서, mysql에 root 계정으로 들어갑니다

``` shell
$ sudo mysql
Enter password:
```

root password를 입력하고, root에 password가 설정되어 있지 않은 경우에는 -p 옵션을 빼줍니다.  
다음은 로컬에서 접속 가능한 사용자를 추가하기 위해, 다음과 같은 명령어를 입력합니다.

``` shell
$ create user '사용자 이름'@'localhost' identified by '비밀번호';
```

그리고 만든 사용자에게 DB에 접근할 수 있는 권한을 부여하기 위해 다음과 같은 명령어 중 하나를 실행합니다.

``` shell
$ grant all privileges on *.* to '사용자 이름'@'localhost'; # 모든 DB에 접근 가능하게 하기
$ grant all privileges on DB이름.* to '사용자 이름'@'localhost'; # 특정 DB에 접근 가능하게 하기
```

그리고 변화를 적용하기 위해, shell에 다음과 같은 명령어를 입력하고

``` shell
$ FLUSH PRIVILEGES;
```

exit명령어로 mysql를 나가면 django에서 mysql을 이용해 DB를 사용할 수 있게 된다.  

-- 참고자료  
https://www.digitalocean.com/community/tutorials/how-to-use-mysql-or-mariadb-with-your-django-application-on-ubuntu-14-04
