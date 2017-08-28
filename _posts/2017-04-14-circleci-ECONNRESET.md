---
layout: post
section-type: post
title: CircleCI에서 NPM - ECONNRESET 관련 에러
category: circleCI
tags: [ 'circleCI','ci' ]
---

원래 잘 작동 중이던 circleCI가, 갑자기 npm install을 하는 과정에서 다음과 같은 에러를 내뿜으며 빌드가 실패되고 말았다.

``` shell
npm ERR! Linux 3.13.0-86-generic
npm ERR! argv "/home/ubuntu/nvm/versions/node/v4.3.0/bin/node"     "/home/ubuntu/nvm/versions/node/v4.3.0/bin/npm" "install"
npm ERR! node v4.3.0
npm ERR! npm  v2.14.12
npm ERR! code ECONNRESET
npm ERR! errno ECONNRESET
npm ERR! syscall read
npm ERR! network read ECONNRESET
npm ERR! network This is most likely not a problem with npm itself
npm ERR! network and is related to network connectivity.
npm ERR! network In most cases you are behind a proxy or have bad network settings.
npm ERR! network
npm ERR! network If you are behind a proxy, please make sure that the
npm ERR! network 'proxy' config is set properly.  See: 'npm help config'
```

이는 원인이 node의 현재 버전이 이와 관련한 버그를 가지고 있었고,  
이를 해결하기 위해서는 버그를 가지고 있지 않은 노드 버전(4이상 이나 5이상의 버전)을 지정해 두면 되는 것이었다.  
그래서 난 구글 검색에 따라 이 버그를 포함하고 있지 않은 버전인 5.11.0 버전을 지정했다.

``` yml
machine:
   node:
       version: 5.11.0
```
해결!  
