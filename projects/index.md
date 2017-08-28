---
layout: project
title: All Projects
excerpt: "A List of Projects"
comments: false
---

## Web

### LinkU, *SoftWare Maestro(http://swmaestro.kr)* [2016.12 ~ 2017.07]

> [https://linkuniversity.me](https://linkuniversity.me)  
> [https://github.com/linkuniversity/LinkU](https://github.com/linkuniversity/LinkU)

#### Overview
- "대학생 연결 모임 서비스" 라는 주제로 대학생들이 부담없이 만나서 맛있는 식사를 함께 하고 이야기 할 수 있는 장을 만드는 것을 목표로 하고있습니다. 대학생들은 여러 모임 유형인 '동아리', '대외활동' 등의 활동을 하고 있지만 꾸준한 참석, 장기적인 것이라는 부담감이 있습니다. 이와 동시에 혼밥족은 점점 늘어나고 있는 추세이고 이를 해결하기 위해 단기적인 대학생 식사 모임을 주선하는 서비스를 만들었습니다.

#### Role

- react, react-router, redux, redux-saga 등을 사용해 SPA(Single Page Application) 구축
- django rest framework를 사용해 restful api 설계
- TDD를 통해 안정적인 서비스 개발(pytest, enzyme)
    - backend는 pytest-django, pytest를 사용해 테스팅
    - frontend는 enzyme을 사용해 테스팅
    - code coverage 사용
    - circleCI를 사용해 Continuous Integration
- git flow을 사용해 팀원들과 협업

<hr>
### Open Search Insight, *SoftWare Maestro(http://swmaestro.kr)* [2016.09 ~ 2016.12]

> [https://bitbucket.org/Jangsunhyuk/smtm_crawler/src](https://bitbucket.org/Jangsunhyuk/smtm_crawler/src)

#### Overview

[크롤링 영상 URL](https://www.youtube.com/watch?v=NNAVqGQi1N4)

마케터들이 가지고 있는 고객 정보 데이터(전화번호)를 기반으로 사용자 공개 데이터(SNS 등)를 크롤링 해서 고객의 데이터를 수집한 다음, 이를 분석해 고객 정보 데이터들의 높은 인구통계학 분포 비율과 공통 관심사 분석을 추출해낸다음 마케터들에게 결과를 제공합니다.  

이를 통해 마케팅에 어려움을 겪고 있는 스타트업 또는 광고 에이전시의 마케터들이 보다
쉽게 고객을 이해하고 더 나은 페이스북 광고 효율 및 마케팅 효과를 얻을 수 있을 것으로 기대됩니다.  
실제 사업화 가능성 검증을 위해 티저 페이지를 오픈한 결과 수십명의 베타 사용자들을 모집할 수 있었습니다.

#### Role
- 트위터, 페이스북 API 기반 데이터 수집과 사전 학습
- Selenuim 활용 페이스북 데이터 수집
- 형태소 분석과 관련 전처리
- TextRank 알고리즘 구현
- 페이스북 Marketing API 활용
- 도달 범위 추출 사용자 분석 API 서버 구현

<hr>
## Game

### ZombieHunter, *GoodGame* [2013.09 ~ 2014.05]

> [https://github.com/wkdtjsgur100/ZombieHunter](https://github.com/wkdtjsgur100/ZombieHunter)
> [https://bitbucket.org/Jangsunhyuk/newzombiehunter/src](https://bitbucket.org/Jangsunhyuk/newzombiehunter/src) (Advanced)

#### Overview
Game Application based cocos2d-x(https://github.com/cocos2d/cocos2d-x/)  
세계적인 오픈소스 게임엔진(cocos2d-x) 분석을 통해 가독성과 확장성이 높은 게임을 제작.

#### Role
- UI/Effect/Tutorial/Ranking 시스템 개발
    - STL을 이용해 적절한 자료구조를 적용했고 스마트 포인터를 통해 효율적인 메모리 관리
- 클래스 구조 설계
    - gof의 디자인 패턴을 활용해 설계. 패턴에 정형화 된 구조보다는 디자인 원칙([SOLID 원칙](https://wkdtjsgur100.github.io/blog/2017/06/09/solid-principle/))과 캡슐화에 신경쓰며 제작
- 게임 내 지형 제작 툴 제작
    - rapidJson이라는 외부 C++ 라이브러리을 사용해 json 통신.
- 리소스 관리 툴 Texture Packer를 사용해 리소스를 효율적으로 제작


<hr>
### MirrorMirror, *GoodGame* [2013.08 ~ 2013.09]

> [https://play.google.com/store/apps/details?id=goodgame.mirrormirror](https://play.google.com/store/apps/details?id=goodgame.mirrormirror)
> [https://github.com/wkdtjsgur100/MirrorMirror](https://github.com/wkdtjsgur100/MirrorMirror)

### Overview
게임 엔진없이 Android SurfaceView 만을 사용해 개발, 출시한 프로젝트.  
첫 android 게임 출시 프로젝트  

### Role
- 팀 공동 기획
- google Admob으로 하단에 광고삽입
- 게임 Effect, Ranking, UI 등 제작

<hr>
## Image Processing (C++)

> [https://github.com/wkdtjsgur100/image-processing](https://github.com/wkdtjsgur100/image-processing)

### Overview
KAIST SOC Robot War(http://www.socrobotwar.org/) 대회에서
색종이 색깔 및 꼭지점 추적 알고리즘 개발(C++, openCV 미사용)

### Role
- [Hough Transform](https://wkdtjsgur100.github.io/blog/2017/01/05/Hough-Transform/) 구현 및 응용
- Canny, Prewitt Edge Detecting 구현
- 이진화 영상 threshold automation detecting 구현
- 색상 인식 알고리즘 구현
- 알고리즘 설계
