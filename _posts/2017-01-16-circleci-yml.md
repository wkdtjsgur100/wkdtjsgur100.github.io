---
layout: post
section-type: post
title: circleci configuration
category: ci
tags: [ 'circle-ci', 'ci' ]
---

> circleci를 프로젝트에 붙이는 방법에 대해서 작성

configuration에 대한 자세한 설명은 https://circleci.com/docs/configuration/ 이 url을 참고하면 된다.

일단 circleci를 붙이기 위해서는 해당 프로젝트 루트 디렉터리에 circle.yml을 추가하게 되면 circleci가 해당 내용에 맞춰

<img alt="success" src = "/images/posts/test_phase.png"/>

circleci는 위의 7단계를 거쳐서 테스트를 진행하게 되는데
각 단계 내부에서는 또 pre, override, post단계가 있게 된다.

그 3가지 단계에 대해서는 아래 이미지가 잘 설명해주는데, 여기서 inferred command란 기본 circleci에서 기본적으로 해주는 명령어들을 말한다. 예를 들어, 파일에 requirements.txt 파일이 있으면 ```pip install -r requirements.txt```을 자동으로 해주는 command를 말한다.

<img alt="success" src = "images/posts/phase.png"/>

위의 설명처럼, inferred command를 실행하기 전(pre), 후(post) 그리고 덮어쓰기(override)를 할 수 있게 된다.
