---
layout: post
section-type: post
title: Zappa + Aurora(MySQL) + django rest framework로 Restful 서버리스 아키텍쳐 구축하기
category: serverless
tags: ['django', 'serverless']
comments: true
---

# 개요

본 글을 보기 전 [왜 Serverless와 Zappa를 사용해야 할까](https://wkdtjsgur100.github.io/why-zappa-and-serverless) 글을 보고 오시면 좋습니다.
> Serverless와 zappa에 대해 이미 잘 알고 계신 분이라면 스킵해도 무방합니다.

# Zappa + Aurora(Serverless RDBMS) + Django Rest Framework

Zappa가 간단한 python 스크립트 정도는 배포하기가 매우 쉽지만, Zappa와 django, mysql을 연동하는 것이 생각보다 해야할 것들이 꽤 있습니다.
그래서 github 레포에 작업을 미리 다 해놓고 그 과정을 작성했습니다.

## github

결과를 먼저 보고 싶으신 분들은 이 github에 들어가서 initial setup 절차를 진행하면 됩니다.

[https://github.com/wkdtjsgur100/zappa-drf-mysql](https://github.com/wkdtjsgur100/zappa-drf-mysql)


# 사전 지식

여기에서는 파이썬 가상환경과 django에 대해서는 자세히 설명하지 않습니다.
아래 링크를 참고하시기 바랍니다.

- python 가상환경: https://wkdtjsgur100.github.io/ubuntu-pyenv-virtualenv-autoenv/
- django 공식 홈페이지: https://docs.djangoproject.com/ko/3.1/intro/
- Zappa github: https://github.com/Miserlou/Zappa

# 실행 환경

버전이 맞지 않으면 제대로 동작하지 않을 수 있습니다. 아래 설명하는 환경에서의 버전은 다음과 같습니다.

- Python 3.8.6
- Zappa 0.52.0
- Django 3.1.4


# 환경 설정

장고 프로젝트를 하나 생성해주겠습니다.

```shell
django-admin startproject zappa_django_mysql
```

다음은 가상환경 설정입니다. 프로젝트에 필요한 패키지들을 가상화 시켜서 별도로 분리해서 관리할 수 있도록 하는 virtualenv, 파이썬 버전을 분리시키는 pyenv를 사용했습니다.

> tip: Pycharm을 사용하고 있다면 [여기](https://www.jetbrains.com/help/pycharm/creating-virtual-environment.html#python_create_virtual_env)를 참고

```shell
pyenv install 3.8.6 # pyenv로 파이썬 버전 3.8.6 설치
pyenv virtualenv 3.8.6 python-3.8.6  # virtualenv를 python-3.8.6을 이름으로 한 가상 환경 설치
pyenv activate python-3.8.6 # python-3.8.6 가상 환경 활성화
```

이제 가상 환경 안으로 들어왔으니, zappa와 django에 필요한 패키지들을 설치합니다.

```shell
pip install zappa==0.52.0
pip install django==3.1.4
pip install zappa-django-utils
```

# 데이터베이스(RDS/Aurora) 설정

AWS Aurora는 serverless 관계형 데이터베이스로, 자동 확장이 가능하고 트래픽이 있을때만 비용을 청구하게 할 수 있습니다.(정해진 시간동안 작동하게 할수도 있습니다.)

- RDS 콘솔에 들어가서 엔진 유형은 Aurora를 선택하고, 용량 유형은 서버리스를 선택합니다.
- 자격 증명 설정의 마스터 이름은 root로 설정하고, 암호도 설정합니다.
- 용량 설정에 보면 추가 조정 구성이 있는데, 다음 시간(분) 동안 비활성 상태 지속 후 컴퓨팅 용량 일시 정지에 체크를 해서 컴퓨팅 용량이 일시 중지된 동안에는 데이터베이스 스토리지에 대해서만 요금이 부과되도록 할 수 있습니다. 오랫동안 작동 안되는 상태에서 요청이 들어가면 인스턴스가 켜지는 동안 시간이 오래걸리기 때문에, 인스턴스를 계속 켜두어야 한다면 aurora 대신 상용 mysql을 쓰는 것이 더 저렴할 수 있습니다.  
- 추가 구성의 초기 데이터베이스 이름을 지정합니다.
- 데이터 베이스 생성을 클릭합니다.
- Aurora serverless 인스턴스는 같은 VPC내에서만 접근할 수 있기 때문에, aws cloud9을 사용하거나 EC2 인스턴스 프록시를 통해서 접근해야합니다.
- aurora는 프리티어가 없습니다. RDS mysql를 먼저 사용해서 프리티어 사용 후 aurora로 데이터를 migration을 하는 것도 좋은 방법입니다.
- 생성을 하게 되면 RDS에 접근할 수 있는엔드포인트를 얻을 수 있습니다. 이 엔드포인트를 django 서버에 연결해 주겠습니다.
- Aurora MySQL 2.04.8이 정식 버전입니다. Aurora MySQL 2.x 버전은 MySQL 5.7과 호환 가능하고, Aurora MySQL 1.x 버전은 MySQL 5.6과 호환됩니다. mysql 8.0은 아직 지원하지 않습니다.
 - 따라서 Aurora를 사용한다면 로컬도 MySQL 버전을 5.7로 맞춰주는 것이 좋을듯 합니다.

# RDS - django 서버 연결하기

가장 먼저 django에서 mysql을 연결할 수 있는 mysql 관련 파이썬 패키지를 설치하고, 설치된 파이썬 패키지를 requirements.txt로 옮기겠습니다.

```
pip install mysqlclient mysql-connector-python
pip freeze > requirements.txt
```

그리고 먼저 settings.py 파일을 local과 production으로 분리하겠습니다. 접속하는 환경별로 다른 셋팅 정보를 지녀야 하기 때문입니다.
zappa_drf_mysql 하위에 settings 패키지를 만들고 그 하위에 기존 settings.py 파일에 있는 내용을 복사해서 base.py를 만들고, local.py와 production.py를 만듭니다.
그리고 production.py에 해당 내용을 반영합니다.

``` py
from base import *

DATABASES = {
    'default' : {
        'ENGINE': 'mysql.connector.django',
        'NAME': '데이터베이스 이름',
        'USER': '데이터베이스 접속 유저명',
        'PASSWORD': '데이터베이스 접속 패스워드',
        'HOST': 'RDS(Aurora) 엔드포인트',
        'PORT': '3306',
    }
}
```

local.py에는 로컬 데이터베이스의 정보를 넣습니다.

``` py
from base import *

DATABASES = {
    'default' : {
        'ENGINE': 'mysql.connector.django',
        'NAME': "database",
        'USER': "root",
        'PASSWORD': "",
        'HOST': '127.0.0.1',
        'PORT': '3306',
    }
}
```

그리고 CORS 호스트 허용을 위해 base.py에 다음을 추가해줍니다.

``` py
ALLOWED_HOSTS = [
    '.execute-api.ap-northeast-2.amazonaws.com',
    'localhost:8000',
]
```

# Zappa 설정

zappa는 먼저 aws 계정이 연동되어야 하기 때문에, aws 계정 연동을 먼저 해야합니다.  
AWS IAM 서비스 콘솔에 들어가서, 접근 권한을 가지고 있는 사용자에 들어가서 보안 자격 증명 - 액세스 키 만들기를 통해 액세스 키 id와 액세스 시크릿 키를 복사해서, `~/.aws/credentials` 파일을 생성하고 IAM 정보를 다음과 같이 넣습니다.

```text
[default]
aws_access_key_id=<AWS 액세스 키 ID>
aws_secret_access_key=<AWS 액세스 시크릿 키>
```

또한 지역 설정을 위해 `~/.aws/config` 파일을 생성하고 다음과 같은 내용을 넣습니다. 지역은 서울 지역 ap-northeast-2로 설정하겠습니다.

```text
[default]
region=ap-northeast-2
```

이제 `zappa init` 명령어를 실행시킵니다.(전부 default로 설정) settings 파일 경로를 입력할때 zappa_django_rds.settings.production을 입력해줍니다.
아래와 같은 zappa_settings.json 파일이 프로젝트 폴더에 생깁니다.

``` json
{
    "dev": {
        "aws_region": "ap-northeast-2",
        "django_settings": "zappa_django_rds.settings.production",
        "profile_name": "default",
        "project_name": "zappa-django-rds",
        "runtime": "python3.8",
        "s3_bucket": "zappa-abccde"
    }
}
```

여기서 s3_bucket은 Lambda에 배포될 소스코드가 업로드되는 S3 버킷입니다.


# Lambda의 vpc config 설정 하기

Aurora와 Lambda는 같은 vpc에 있어야 Lambda 서버에서 Aurora에 접근할 수 있습니다. 그렇지 않으면 NAT를 사용해야 하는데, 비용 문제도 있어서 특별한 경우 아니면 같은 VPC에 있는게 좋습니다.  
Aurora 콘솔에 들어가서, Aurora 데이터베이스가 속한 VPC의 Subnet Id들을 복사합니다. 그리고 `zappa_settings.json` 파일에 다음과 같은 내용을 추가합니다. SecurityGroupIds는 Lambda가 속하는 security group id를 작성합니다.

``` json
{
    "dev": {
        ...
        "vpc_config": {
            "SubnetIds": ["subnet-abcd1234", "subnet-cdef1234", "subnet-efgk5678"],
            "SecurityGroupIds": ["sg-1234naf"]
        }
    }
}
```

# django-rest-framework 설정하기

django만 이용한다면 이 과정을 생략해도 좋습니다.  
drf를 먼저 설치해주고 settings/base.py의 INSTALLED_APPS에 rest_framework를 추가해줍니다.

```
pip install djangorestframework
```

# static 파일(css, js, html 등) 설정하기

django에서 static 파일들이 쓰여야 할 경우가 있습니다.(어드민 같은 경우에) 이 static 파일을 S3에 저장해두고, 접근할 수 있도록 하겠습니다. 가격은 GB당 0.023 USD이기 때문에 static 파일에 대한 비용은 거의 나오지 않습니다.

우선 AWS S3 콘솔에서 s3 버킷을 하나 만들겠습니다. 버킷을 만드는 방법은 아주 간단합니다. 
- S3 콘솔에 들어가서 버킷 만들기를 클릭합니다.
- 버킷 이름을 입력합니다. my-django-static-files로 했다고 가정하겠습니다.
- 버킷을 생성할때 모든 퍼블릭 액세스 차단을 체크 해제합니다.
- 버킷 만들기를 완료합니다.

이제 static 파일들을 위한 S3 버킷을 만들었으니, django에서도 작업이 필요합니다. 여러 패키지가 있겠지만 가장 많이 쓰이는 django-storages를 사용하겠습니다.  
  
먼저 파이썬 패키지를 설치합니다.

```
pip install django-storages
```

그리고 STATIC 파일들에 대한 정보를 setting 파일에 추가해야합니다. 아래와 같은 내용을 production.py에 넣습니다.

```
AWS_STORAGE_BUCKET_NAME = "my-django-static-files"
AWS_S3_REGION_NAME = "ap-northeast-2"
AWS_S3_HOST = f"s3.{AWS_S3_REGION_NAME}.amazonaws.com"
AWS_S3_CUSTOM_DOMAIN = f"{AWS_STORAGE_BUCKET_NAME}.{AWS_S3_HOST}"
STATIC_URL = f"https://{AWS_S3_CUSTOM_DOMAIN}/"
STATICFILES_STORAGE = "zappa_drf_mysql.storages.ZappaS3Boto3Storage"
STATICFILES_DIRS = [os.path.join(BASE_DIR, "static")]
DEFAULT_FILE_STORAGE = "zappa_drf_mysql.storages.ZappaS3Boto3Storage"

AWS_ACCESS_KEY_ID = <aws access key id>
AWS_SECRET_ACCESS_KEY = <aws secret access key>
```
위의 내용은 구체적인 건 이 [문서](https://django-storages.readthedocs.io/en/latest/backends/amazon-S3.html)를 참고하면 됩니다.  

문서에 따르면 STATICFILES_STORAGE와 DEFAULT_FILE_STORAGE에 `storages.backends.s3boto3.S3Boto3Storage`가 들어가야 하지만, `zappa_drf_mysql.storages.ZappaS3Boto3Storage`가 되어 있습니다. [그 이유](https://github.com/jschneier/django-storages/issues/606)는 django-storages에서 AWS security token을 환경변수에서 자동으로 가져오게 되어 있는데, AWS Lambda에서 제공하는 환경변수 정보가 S3 접근에 대한 token과 같은 credential 정보를 제대로 가져오지 못하는 문제라고 합니다.  
그래서 다음과 같은 클래스를 정의 후 이 클래스를 참조 했습니다.

``` py
# zappa_drf_mysql/storages.py

from storages.backends.s3boto3 import S3Boto3Storage


class ZappaS3Boto3Storage(S3Boto3Storage):
    def _get_security_token(self):
        return None
```

이제 static 파일 관련 셋팅도 끝이 났습니다.

# 배포하기

이제 드디어 배포할 준비가 끝났습니다.  
`zappa deploy dev` 명령으로 배포해보겠습니다.  
배포가 실패한다면 `zappa tail dev` 명령어로 배포한 Lambda에서 실패 로그를 확인할 수 있습니다.  
배포가 성공적으로 끝난다면 다음과 같은 명령어를 사용해 static 파일을 collect 하는 작업이 필요합니다.

```
zappa manage dev "collectstatic --noinput"
```
 
# 데이터베이스 migrate

변경된 데이터베이스를 적용하는 migrate 작업이 필요하다면 다음 명령어를 실행합니다.

```
zappa manage dev migrate
```

# 코드 수정 이후의 배포

이미 배포된 상태에서 코드를 수정하고 다시 배포하려면 다음 명령어를 수행합니다.

```
zappa update dev
```

# 배포 된 것 내리기

배포된 내용들을 전부 내리고 싶을 경우에는 다음 명령어를 사용합니다.

```
zappa undeploy dev
```

# Notes

Lambda는 속한 VPC가 아닌 외부 네트워크와 통신하기 위해서는 NAT가 필요합니다. NAT 비용이 비싸기 때문에 비용을 고려할때 이것도 고려하는게 좋습니다.

# 참고

- https://romandc.com/zappa-django-guide/
