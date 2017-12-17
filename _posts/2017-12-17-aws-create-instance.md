---
layout: post
section-type: post
title: Mac OS X에서 AWS 프리티어 EC2 instance 생성 및 접속하기(Ubuntu)
category: aws
tags: [ 'aws' ]
---

# AWS (Amazon Web Service)란?

> AWS는 간단히 말해 아마존에서 제공하는 여러 서비스들을 의미합니다. 저희는 그 중에서도 EC2라는 서비스의 instance를 생성 및 접속하는 방법에 대해서 알아보겠습니다.

# EC2 instance?

> EC2 instance는 쉽게 생각해서 우리가 일반적으로 보는 PC나 서버와 똑같은 형태이며 Linux나 Windows가 설치되어 있습니다. 가상 서버이기 때문에 모니터에는 직접 연결할 수 없고 터미널 또는 원격 데스크톱 연결로 접속해야 합니다.(ssh 접속)

EC2에 대한 상세한 내용은 [pyrasis.com][http://pyrasis.com/book/TheArtOfAmazonWebServices]라는 온라인 북을 보시면 더 자세히 아실 수 있습니다.

# EC2 instance 생성

먼저 [aws console](https://console.aws.amazon.com/console/home)에 접속해 회원가입 및 로그인을 하면, 우측 상단에 오하이오 dropdown이 보입니다. 이것을 클릭해서, 아래 사진과 같이 서울로 지역을 바꿔줍니다. 이는 AWS에서 인스턴스를 어느 지역에 생성할 지를 선택하는 것을 의미합니다.  

![region](/images/posts/region.png)

이제 아래 사진과 같이 좌측 상단의 `서비스` 버튼을 클릭한 다음, EC2 메뉴를 선택합니다.  

![EC2](/images/posts/ec2.png)

그러면 아래와 같은 페이지가 보이는데 좌측의 `인스턴스` 메뉴를 누른다음, 중앙에 보이는 `인스턴스 시작` 버튼을 눌러줍시다.  

![instance start](/images/posts/instance_start.png)

OS를 선택할 수 있는 창이 나옵니다. 우분투를 선택해줍시다.  
![aws1](/images/posts/aws_1.png)

인스턴스 유형을 선택할 수 있는데, 이미 프리티어로 선택이 되어 있으니 `검토 및 선택` 버튼을 바로 눌러줍시다.  
![aws2](/images/posts/aws_2.png)

이제 세부 항목들을 설정할 수 있습니다. 경험 상 스토리지의 크기는 8GB가 부족할 때가 많기 때문에, 30GB로 설정해주는 것이 정신건강에 좋은 것 같습니다.  
스크롤을 아래로 내려, `스토리지` 옵션의 `스토리지 편집` 버튼을 눌러줍니다.  

![aws3](/images/posts/aws_3.png)

스토리지 크기를 `30GB`로 설정해줍시다.  

![aws4](/images/posts/aws_4.png)

마지막으로 우측 하단의 `시작` 버튼을 누르면 다음과 같은 창이 뜹니다. `새 키 페어 생성`을 선택한 다음, 키 페어 이름을 입력해 줍니다. (제 경우엔 `mykeypair`로 입력해 주었습니다.)  
`키 페어 다운로드` 버튼을 눌러 키 페어를 다운받고, `인스턴스 시작` 버튼을 눌러줍시다.  

![aws5](/images/posts/aws_5.png)

이제 처음의 인스턴스 메뉴를 보게 되면, 인스턴스가 생성된 것을 확인하실 수 있습니다.  

![aws6](/images/posts/aws_6.png)

가장 오른쪽의 `IPv4 퍼블릭 IP` 밑의 ip를 이용해 ssh 접속을 하면 됩니다.

# EC2 instance ssh 접속

인스턴스 셋팅이 끝났으면 shell에서(Mac인 경우엔 iterm이나 zshell 혹은 bash shell, Window인 경우엔 putty를 사용하시면 됩니다.)

``` shell
sudo ssh -i key/pair/root/mykeypair.pem ubuntu@[IP Address]
```

> 여기서 IP Address는, 인스턴스 페이지에서 확인 하셨던 `IPv4 퍼블릭 IP`을 의미합니다.

명령어를 이용해 ssh 접속을 하시면 됩니다. 접속을 시도하면, 가장 초기엔 다음과 같은 명령어가 뜹니다.

``` shell
jang@jang-3:~ » sudo ssh -i Desktop/key/mykeypair.pem ubuntu@[IP Address]                                      
The authenticity of host xx.xxx.xxx cant be established.
ECDSA key fingerprint is SHA256:fUIbFrxcYjElPQvGsdfASDFASDFDSsdfsdfASDf.
Are you sure you want to continue connecting (yes/no)?
```

yes를 입력해, 접속합니다.
