---
layout: post
section-type: post
title: Ubuntu 16.04 Firefox Downgrade
category: ubuntu
tags: [ 'ubuntu' ]
---

# Firefox Downgrade

Firefox 55.0.2로 downgrade를 하고 싶다고 가정하자.

``` shell
# 기존에 설치되어 있는 firefox를 삭제
sudo apt-get purge firefox
# wget을 이용해서 설치 압축 파일을 가져오자.
sudo wget http://ftp.mozilla.org/pub/firefox/releases/55.0.2/linux-$(uname -m)/en-US/firefox-55.0.2.tar.bz2
# 압축 해제
sudo tar -xjf firefox-55.0.2.tar.bz2

# 기존에 firefox 폴더를 완전히 삭제해주자.
sudo rm -rf /opt/firefox
# 압축이 풀린 firefox 폴더를 opt에 이동시켜준다
sudo mv firefox /opt/

# 기존 firefox binary를 삭제시켜주고, 새로 설치된 binary를 /usr/bin/에 바로가기를 만든다.
sudo rm -rf /usr/bin/firefox
sudo ln -s /opt/firefox/firefox /usr/bin/firefox

```

firefox version을 확인해보면, 제대로 설치된 것을 확인할 수 있다.

``` shell
$ firefox -version
firefox 55.0.2
```
