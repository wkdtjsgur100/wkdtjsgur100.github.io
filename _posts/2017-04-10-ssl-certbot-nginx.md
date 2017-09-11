---
layout: post
section-type: post
title: Let's Encrypt를 사용해 무료로 https(ssl)를 붙이는 방법(Ubuntu 14.04, certbot, 2017.09 updated)
category: ssl
tags: [ 'ssl' ]
---

## 소개

Let's Encrypt는 무료 SSL 인증서를 얻고 설치하는 쉬운 방법인 새로운 Certificate Authority(CA) 입니다.  
그 때문에 웹 서버에서 https를 암호화되는 것을 가능하게 합니다. 필요한 단계들을 대부분 자동화 시켜주도록 하는 certbot 소프트웨어를 이용함으로서 이 처리를 간단하게 할 수 있습니다. 현재, 인증서를 설치하고 얻는 전체의 과정은 아파치 웹서버 에서만 완전히 자동화 할 수 있지만, 웹서버 소프트웨어의 석택을 신경쓰지 않고 매뉴얼 대로 설치할 수 있는 무료 SSL 인증서를 쉽게 얻는데 사용할 수 있도록 암호화 해봅시다.

이 튜토리얼에서, 당신이 certbot-auto를 사용하는 방법을 볼 것입니다. ubuntu 14.04에서 nginx와 무료 ssl 인증서를 획득하는 것을 해보겠습니다. 그리고 자동적으로 ssl 인증서 갱신을 하는 방법도 보여줄 것입니다.

# 사전준비

이 튜토리얼을 따라가기 전에, 몇몇 준비들을 해야합니다.

- sudo 권한을 가진 non-root 사용자 Ubuntu 14.04 서버일 경우, [Ubuntu 14.04 튜토리얼 초기 서버 설정](https://www.digitalocean.com/community/articles/initial-server-setup-with-ubuntu-14-04) 을 따라가 사용자 계정을 설정하는 방법에 대해서 배울 수 있습니다.
- Nginx 설치를 위해, [Ubuntu 14.04 LTS에서 Nginx 설치하는 방법](https://www.digitalocean.com/community/tutorials/how-to-install-nginx-on-ubuntu-14-04-lts)을 보면 됩니다.
- 증명서를 붙일 등록된 도메인을 컨트롤해야합니다. 아직 등록된 도메인 명을 가지고 있지 않다면, Namecheap, GoDaddy 같은 곳에서 도메인을 하나 등록하세요.
- 서버의 공인 IP 주소에 도메인을 가르키는 DNS A 레코드는 Let's Encrypt가 인증서를 발급하는 도메인을 소유하고 있는지 확인하는 방법 때문에 필요로 합니다. 예를 들어서, example.com에 인증서를 붙이려고 한다면, 작동을 위한 유효성 확인 프로세스를 위해 서버를 결정해야 합니다. 설정은 example.com과 www.example.com을 도메인 이름으로서 쓸것이고, 둘다 DNS 레코드들이 필요합니다.  

일단 모든 사전 준비를 끝냈다면, Let's Encrypt 클라이언트 소프트웨어인 Certbot을 설치하러 가봅시다.

### 단계 1 - certbot 설치

SSL 인증서를 얻기 위한 Let's Encrypt를 사용하는 가장 첫번째로 해야할 일은 `certbot` 소프트웨어를 서버에 설치하는 것입니다. certbot 개발자들은 소프트웨어의 가장 최신버전으로 우분투 소프트웨어 저장소를 유지시킵니다. 그 이유는 certbot이 매우 활성화된 개발을 진행중이기 때문에 우분투에서 제공되는 것보다 더 새로운 certbot을 설치 위해서는 이 저장소를 사용하는 것이 좋습니다.  
먼저, 저장소를 추가합니다

``` shell
$ sudo add-apt-repository ppa:certbot/certbot
```

적용하려면 ENTER를 눌러야합니다. 그 이후에, 새로운 저장소 패키지 정보를 가져오기 위해 패키지 리스트를 업데이트 합시다.

``` shell
$ sudo apt-get update
```

마지막으로, apt-get으로 certbot을 설치합시다

``` shell
$ sudo apt-get install python-certbot-nginx
```

`cerbot` Let's Encrypt 클라이언트를 이제 사용할 수 있습니다.

### 단계 2 - nginx 설정

Certbot은 Nginx SSL 설정을 자동적으로 할 수 있지만, 설정에서 올바른 server block을 찾을 수 있게 해 주어야 합니다. 이는 인증서를 요청하는 도메인을 매칭시키는 직관적인 `server_name`을 찾기 위해서 하는 것입니다. 새로운 nginx 설치로 시작한다면, 기본 설정파일을 업데이트 할 수 있습니다.

``` shell
$ sudo vim /etc/nginx/sites-available/default
```

server_name이 존재하는 라인을 찾아갑시다.

``` yml
# /etc/nginx/sites-avilable/default

server_name localhost;
```

도메인 명의 localhost를 다음과 같이 바꿔줍니다.

``` yml
# /etc/nginx/sites-avilable/default

server_name example.com www.example.com;

```

작성된 파일을 저장하고 나갑니다. 설정 편집을 다음과 같은 명령어로 제대로 작성되었는지 확인합니다.

``` shell
$ sudo nginx -t
```

에러가 안생기면, Nginx를 reload 해줍시다.

``` shell
$ sudo service nginx reload
```

Certbot은 올바른 server block을 찾고 업데이트를 할 수 있게 될것입니다. 이제 HTTPS 트래픽을 승인하는 방화벽을 업데이트해봅시다.

### 단계 3 - SSL 인증서 획득

Cert bot은 SSL 인증서를 획득하기 위해서 다양한 플러그인을 통해 여러 방법들을 제공합니다. nginx 플러그인은 필요할 때마다 설정파일을 갱신하고 nginx를 다시 설정하도록 해줍니다.

``` shell
$ sudo certbot --nginx -d example.com -d www.example.com
```

`--nginx` 플러그인 으로 certbot을 작동시킵니다. 그리고 `-d` 옵션으로 인증서를 유효하게 하려는 지정하게 합니다.

certbot을 구동하는게 처음이라면, 서비스의 약관에 동의하고 이메일 주소를 입력해야합니다. 그렇게 하고 나면, certbot은 Let's Encrypt 서버와 통신할 것이고, 인증서를 요청하고 있는 도메인을 제어하는지 확인하기 위해서 요청을 실행합니다.

성공적이라면, certbot은 HTTPS 설정을 어떻게 할지 물어봅니다.

``` shell
Please choose whether HTTPS access is required or optional.
-------------------------------------------------------------------------------
1: Easy - Allow both HTTP and HTTPS access to these sites
2: Secure - Make all requests redirect to secure HTTPS access
-------------------------------------------------------------------------------
Select the appropriate number [1-2] then [enter] (press 'c' to cancel):
```

ENTER를 입력하면 선택이 됩니다. 설정이 업데이트 된다면, Nginx는 새로운 셋팅들을 선택해 갱신할 것입니다. certbot은 처리가 성공적으로 되었는지 어디에 인증서가 저장되는지 말해주는 메세지를 마무리 해줄것입니다:

``` shell
IMPORTANT NOTES:
 - Congratulations! Your certificate and chain have been saved at
   /etc/letsencrypt/live/example.com/fullchain.pem. Your cert will
   expire on 2017-10-23. To obtain a new or tweaked version of this
   certificate in the future, simply run certbot again with the
   "certonly" option. To non-interactively renew *all* of your
   certificates, run "certbot renew"
 - Your account credentials have been saved in your Certbot
   configuration directory at /etc/letsencrypt. You should make a
   secure backup of this folder now. This configuration directory will
   also contain certificates and private keys obtained by Certbot so
   making regular backups of this folder is ideal.
 - If you like Certbot, please consider supporting our work by:

   Donating to ISRG / Lets Encrypt:   https://letsencrypt.org/donate
   Donating to EFF:                    https://eff.org/donate-le
```

이제 인증서는 다운되었고, 설치되었고, 설정 되었습니다. `https://`를 사용해서 웹사이트를 새로고침 해보세요. 그리고 브라우저에 안전한 사이트라고 뜨는지 확인해 봅시다. 사이트가 적절한 안전한 사이트인지 확인하려면, 초록색 자물쇠 아이콘으로 되어있는 것이 표시가 됩니다.

### 단계 4 - Diffie-Hellman Parameters 업데이트

[SSL Labs Server Test](https://www.ssllabs.com/ssltest/)를 사용해 서버를 테스트 한다면, Diffie-Hellman parameters가 취약해 B grade를 받을 것입니다. 이는 서버와 사용자들 간에 초기 key를 교환하는 안전성에 영향을 미칩니다. 새로운 `dhparam.pem`파일을 만들어서 server block에 추가함으로서 이를 수정해 봅시다.

`openssl`을 사용해서 만듭시다.

``` shell
sudo openssl dhparam -out /etc/ssl/certs/dhparam.pem 2048
```

몇분정도 걸릴 것입니다. 이게 다 되면, server block이 포한되어있는 nginx 설정파일을 열어봅시다. 저희 예제에선, default config file이겠군요.

``` shell
$ sudo vim /etc/nginx/sites-available/default
```

server block안에 어디든 상관없이 다음과 같은 행을 추가합니다.

``` yml
# /etc/nginx/sites-available/default
. . .
ssl_dhparam /etc/ssl/certs/dhparam.pem;
```

에디터를 저장하고 나갑니다. 설정을 확인합니다.

``` shell
$ sudo nginx -t
```

어떤 에러도 없으면, nginx를 리로드 해줍니다.

``` shell
sudo service nginx reload
```

이제 사이트가 좀더 안전해졌습니다. 이제 A 등급을 받게 될것입니다.

### 단계 5 - 자동 갱신 설정

Let's Encrypt's 인증서들은 단지 90일 동안만 유효합니다. 이유는 사용자가 갱신 프로세스를 자동화하도록 권장하기 위한 것입니다. 만료된 인증서들을 체크할 명령어들을 실행하도록 셋팅할 필요가 있고, 자동적으로 이들을 갱신할 필요도 있습니다.

매일 갱신 확인을 작동시키기 위해, `cron`을 사용할 것입니다. 이것은 주기적인 일을 수행하는 표준 시스템 서비스 입니다. crontab이라고 불리는 파일을 열어서 편집하는 일을 하는 것을 cron이라고 합니다.

``` shell
$ sudo crontab -e
```

텍스트 에디터가 디폴트 crontab, 즉 내부에 몇몇 도움말들이 적혀있는 텍스트 파일을 열것입니다. 다음과 같은 행을 파일의 끝에 붙여넣고, 저장하고 종료합시다:

```
. . .
15 3 * * * /usr/bin/certbot renew --quiet
```

`15 3 * * *`이라는 부분은 "매일 오전 3:15분에 다음과 같은 명령을 실행하라" 라는 뜻입니다. 이 시간은 어떤거든 상관 없습니다.  

그리고 cerbot의 renew 명령어는 시스템에 설치된 모든 인증서들을 활인하고 30일 이내에 만료되도록 설정된 인증서를 업데이트 합니다. `--quiet`은 사용자 인력을 기다리거나 정보를 출력하지 말라는 뜻입니다.  

`cron`은 매일 이 명령어를 이제 수행할 것입니다. 모든 설치된 인증서들은 자동적으로 만료되기 전 또는 30일이 지난 것들은 갱신되고 reload될 것입니다.

### 결론

이 튜토리얼은 Let's Encrypt client certbot을 설치해보는 튜토리얼이었습니다. SSL 인증서를 다운로드하고, 이런 인증서들을 사용하는 nginx를 설정하고, 자동적으로 인증서 갱신을 하는 것을 셋팅했습니다. certbot에 대한 다른 질문이 있다면, [이 문서](https://certbot.eff.org/docs/)가 시작하기에 좋은 자료가 될것입니다.

- 출처
 - https://www.digitalocean.com/community/tutorials/how-to-secure-nginx-with-let-s-encrypt-on-ubuntu-14-04
