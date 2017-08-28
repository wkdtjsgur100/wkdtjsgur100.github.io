---
layout: post
section-type: post
title: Ubuntu 14.04에서 무료로 Nginx를 ssl 암호화하는 방법(certbot)
category: ssl
tags: [ 'ssl' ]
---

암호화 할것은 무료 SSL 인증서를 얻고 설치하는 쉬운 방법인 새로운 Certificate Authority(CA) 입니다.  
그때문에 웹 서버에서 https를 암호화되는 것을 가능하게 합니다. 필요한 단계들을 대부분 자동화 시켜주도록 하는 certbot 소프트웨어를 이용함으로서 이 처리를 간단하게 할 수 있습니다. 현재, 인증서를 설치하고 얻는 전체의 과정은 아파치 웹서버 에서만 완전히 자동화 할 수 있지만, 웹서버 소프트웨어의 석택을 신경쓰지 않고 매뉴얼 대로 설치할 수 있는 무료 SSL 인증서를 쉽게 얻는데 사용할 수 있도록 암호화 해봅시다.

이 튜토리얼에서, 당신이 certbot-auto를 사용하는 방법을 볼 것입니다. ubuntu 14.04에서 nginx와 무료 ssl 인증서를 획득하는 것을 해보겠습니다. 그리고 자동적으로 ssl 인증서 갱신을 하는 방법도 보여줄 것입니다. 만약 다른 웹서버를 구동중이라면, 여러분의 설정에 맞는 인증서를 사용하는 법을 알려주는 webserver의 문서를 따라가세요

## 사전준비

클라이언트 소프트웨어를 암호화 사는 certbot-auto를 설치합시다.

### 단계 1 - 클라이언트를 암호화 하는 것을 설치합니다.  

가장 먼저 서버에서 certbot-auto 소프트웨어를 설치하는 것이 ssl 인증서를 얻을 수 있는 것을 하는 것입니다. 현재로서, certbot-auto 클라이언트를 설치할 수 있는 가장 좋은 방법은 EFF의 다운로드 사이트에서 이것을 다운받는 방법이 있습니다. 그 클라이언트는 자동적으로 설치 후에 필요한 것들을 사용가능하게 업데이트 할 수 있도록 해줍니다.

다음과 같이 타이핑해서 /usr/local/bin에 client를 암호화하는 cretbot-auto를 다운로드 할 수 있습니다.

``` shell
$ cd /usr/local/sbin
$ sudo wget https://dl.eff.org/certbot-auto
```

그리고 다음과 같이 입력하여 실행 가능한 스크립트로 만듭니다.

``` shell
$ sudo chmod a+x /usr/local/sbin/certbot-auto
```

certbot-auto 클라이언트는 이제 사용할 준비가 되었습니다.

### 단계 2 - 인증서 획득

SSL 인증서를 얻는 webroot 플러그인을 사용하는 방법에 대해서 알아봅시다.

#### Webroot 플러그인을 사용하는 방법

webroot 플러그인은 validation을 위한 Let's Encrypt 클라이언트에 의해서 열리게 되는 문서 루트 내부의 /.well-known 디렉터리에 특정한 파일을 위치시키면 작동합니다. 개인의 설정에 따라서, /.well-known 디렉터리에 접근하는 것을 명시적으로 허용해야 할 수도 있습니다.  

만약 Nginx가 아직 설치되어 있지 않다면, 다음과 같은 명령어를 입력합니다.

``` shell
$ sudo apt-get update
$ sudo apt-get install nginx
```

validation을 위한 certbot-auto 에 접근 가능한 디렉터리를 확실히 하기 위해서, nginx 설정을 빠르게 바꿔보겠습니다. 기본적으로, /etc/nginx/sites-available/default에 위치 되어있습니다. 우리는 nano를 사용해서 이것을 편집하겠습니다.

``` shell
$ sudo nano /etc/nginx/sites-available/default
```

내부의 서버 block에, 이 location block을 추가합니다.

```
server {
        . . .

        location ~ /.well-known {
                allow all;
        }

        . . .
}
```

또한 webroot 플러그인을 사용하는 것이 필요한 경로 같이 루트 지시어를 검색해서 문서 루트가 설정되어 있는 곳을 찾고 싶을 것 입니다. 만약에 기본 설정 파일을 사용하는 중이라면, 루트는 /usr/share/nginx/html일 것입니다.

다음과 같이 설정 파일에 에러가 있는지 확인합니다.

``` shell
$ sudo nginx -t
```

모든 것이 문제가 없으면, 이제 nginx를 재시작합니다.

``` shell
$ sudo service nginx restart
```

webroot-path를 알았으니, 이 명령어들로 ssl 인증서를 요청하는 webroot 플러그인을 사용할 수 있습니다. -d 옵션을 사용해서 도메인 이름을 지정해주고 있습니다. 여러개의 도메인 이름들(예를 들어 example.com과 www.example.com)을 작동시키는 single cert를 원한다면, 대부분의 높은 레벨 도메인으로 시작함으로서 이것들 전부를 포함시켜줄 수 있습니다. 또, 하이라이트 된 부분을 적절한 webroot 경로와 도메인 이름들로 대체하세요.  

``` shell
$ certbot-auto certonly -a webroot --webroot-path=/usr/share/nginx/html -d example.com -d www.example.com
```

참고: certbot-auto 소프트웨어는 superuser 권한을 필요로 하기에, sudo를 최근에 사용하지 않았었다면 패스워드를 입력해야 할 필요가 있습니다.

certbot-auto 초기화 후에, 몇몇 정보들을 입력되어야 합니다. 정확한 prompt들은 이전에 certbot-auto 를 사용했었는지에 대해 여러모로 의존적일 것이지만, 처음부터 단계별로 설명해 드리겠습니다.  

1. prompt에서, 공지와 잃어버린 키 발급을 위해 email 주소를 입력하세요.
2. Let's Encrypt 구독 동의서에 동의 해야 합니다.
3. 모든 것이 끝나면, 다음과 같은 메세지가 표시 될 것입니다.

``` text
Output:
IMPORTANT NOTES:
 - If you lose your account credentials, you can recover through
   e-mails sent to sammy@digitalocean.com
 - Congratulations! Your certificate and chain have been saved at
   /etc/letsencrypt/live/example.com/fullchain.pem. Your
   cert will expire on 2016-03-15. To obtain a new version of the
   certificate in the future, simply run Let's Encrypt again.
 - Your account credentials have been saved in your Let's Encrypt
   configuration directory at /etc/letsencrypt. You should make a
   secure backup of this folder now. This configuration directory will
   also contain certificates and private keys obtained by Let's
   Encrypt so making regular backups of this folder is ideal.
 - If like Let's Encrypt, please consider supporting our work by:

   Donating to ISRG / Let's Encrypt:   https://letsencrypt.org/donate
   Donating to EFF:                    https://eff.org/donate-le
```

방화벽 참고: Filed to connect to host for DVSNI challenge 같은 에러를 받았다면 포트 80번과 443번 TCP 트래픽을 허용하는 것이 설정되어 있어야 합니다.

#### 인증 파일들

인증서를 얻은 뒤에는, PEM 인코딩된 파일들이 생성되게 됩니다.
- cert.pem : 도메인 인증서
- chain.pem : Let's Encrypt chain 인증서
- fullchain.pem : cert.pem과 chain.pem이 결합된 것.
- privkey.pem : 인증서 개인 키

생성된 인증 파일들의 위치를 인지하는 것은 중요하고, 웹서버 설정에 이것들을 사용할 수 있습니다. 그 파일들은 /etc/letsencrypt/archive 에 서브 디렉터리에 위치하고 있습니다. 하지만, Let's Encrypt 클라이언트는 /etc/letsencrypt/live/your_domain_name 디렉터리에 있는 대부분의 최근 인증서 파일들에 상징적인 링크들을 생성합니다.  

다음과 같은 명령어로 파일이 존재하는지 확인 할 수 있습니다

``` shell
$ sudo ls -l /etc/letsencrypt/live/your_domain_name
```

출력결과는 이전에 언급되었던 4개의 인증 파일들입니다. 순간, 인증 파일로서 fullchain.pem을 사용하는 웹서버를 설정할 것이고, 인증 키 파일 로서 privkey.pem을 사용합니다.

#### Strong Diffie-Hellman Group 생성하기

보안성 증가를 위해서, strong Diffie-Hellman group을 생성해야 합니다. 2048-bit 그룹을 생성하기 위해서, 다음과 같은 명령어를 사용합니다.

``` shell
$ sudo openssl dhparam -out /etc/ssl/certs/dhparam.pem 2048
```

### 단계 3 - 웹 서버에서 TLS/SSL 설정하기(nginx)

서버 block을 포함하는 nginx 설정을 해봅시다. 다시, /etc/nginx/sites-available/default을 편집 합니다.

``` shell
$ sudo vim /etc/nginx/sites-available/default
```

server block을 찾습니다. 80포트를 listion 하는 server block을 설정하는 라인들을 comment out 또는 delete 합니다. 다음의 두줄은 삭제 되어야 합니다.

``` text
listen 80 default_server;
listen [::]:80 default_server ipv6only=on;
```

대신 우리는 SSL을 사용가능하게 하기 위해서 443 포트를 여는 server block을 설정 할 것 입니다. ```server {``` block 안에, 다음과 같은 코드를 작성합니다.

``` text
listen 443 ssl;

server_name example.com www.example.com;

ssl_certificate /etc/letsencrypt/live/example.com/fullchain.pem;
ssl_certificate_key /etc/letsencrypt/live/example.com/privkey.pem;
```

마지막으로, server block 밖에, http를 https로 redirect 하는 server block을 추가 합니다.

``` shell
server {
    listen 80;
    server_name example.com www.example.com;
    return 301 https://$host$request_uri;
}
```

다음과 같은 명령어로 error를 확인한 다음, nginx를 재시작 합니다.

``` shell
$ sudo nginx -t # error check
$ sudo service nginx restart # nginx 재시작
```

그리고 https가 제대로 작동하는지 다음과 같이 웹브라우저에 입력해서 확인 할 수 있습니다.

``` text
In a web browser:
https://www.ssllabs.com/ssltest/analyze.html?d=example.com
```

### 단계 4 - 자동 갱신 설정

Let's encrypt 인증서는 90일 동안 유효 합니다. 하지만 여유를 좀 두기 위해서 60일에 한번씩 인증서를 갱신 하는 것을 추천합니다. certbot-auto 클라이언트의 renew option을 실행함으로서 인증서를 갱신 할 수 있습니다.

모든 설치된 도메인들에 대해서 갱신을 수행하고 싶다면 이 명령어를 사용합니다.

``` shell
certbot-auto renew
```

인증서를 최근에 설치했었기 때문에, 이 명령어는 만료일자를 확인하고  인증서가 아직 갱신 되지 않았다는 걸 알리는 메세지가 출력 됩니다. 출력은 다음과 같이 나타 날 것입니다.

``` text
Output:
Checking for new version...
Requesting root privileges to run letsencrypt...
   /home/sammy/.local/share/letsencrypt/bin/letsencrypt renew
Processing /etc/letsencrypt/renewal/example.com.conf

The following certs are not due for renewal yet:
  /etc/letsencrypt/live/example.com/fullchain.pem (skipped)
No renewals were attempted.
```

여러 개의 도메인에 대해 인증서가 묶여 있는 것을 생성 되었다면 base 도메인 이름이 출력에 나오게 됩니다. 하지만 갱신은 이 인증서를 포함한 모든 도메인에 대해 유효합니다.

기간이 지나지 않을 인증서를 확인하기 위한 실험적인 방법은 주기적으로 자동화된 갱신 명령어를 실행하는 cron job을 만드는 것입니다. 갱신은 가장 먼저 만료 일자를 확인하고 인증서가 만료일자로 부터 30일보다 적게 되어있는 인증서인지 확인하고 실행합니다. 이는 매일 혹은 매주 실행하는 cron job을 만드는 것이 안전합니다. 예를 들어 봅시다

매주 갱신 명령어를 실행할 새로운 일을 만드는 crontab을 편집해 봅시다.

``` shell
$ sudo crontab -e
```

다음과 같은 line들을 추가합니다.

``` text
crontab entry
30 2 * * 1 /usr/local/sbin/certbot-auto renew >> /var/log/le-renew.log
35 2 * * 1 /etc/init.d/nginx reload
```

저장하고 나갑니다. 이것은 매일 월요일 새벽 2:30분에 cerbot-auto renew 명령어를 실행하고, 2:35에 nginx를 reload 하는 cron job을 생성하는 것입니다. 명령어에 대한 결과는 /var/log/le-renewal.log에 log file을 생성해 출력합니다.

### 결론

이제 우리의 웹서버는 무료 Let's Encrypt TLS/SSL 인증서를 안전하게 https 컨텐츠로 제공하게 되었습니다!

-- 참고 자료  
https://www.digitalocean.com/community/tutorials/how-to-secure-nginx-with-let-s-encrypt-on-ubuntu-14-04  
