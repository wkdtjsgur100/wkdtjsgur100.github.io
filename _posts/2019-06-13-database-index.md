---
layout: post
section-type: post
title: 데이터베이스 인덱스 개념 정리(Clustered Index, Non-clustered Index, Primary Index, Secondary Index 등..)
category: database
tags: [ 'database' ]
comments: true
---

> 데이터베이스 인덱스는 대용량의 DB를 다루다보면 필수적인 개념이기도 하고, 면접 질문으로도 자주 등장한다. 인덱스에 대한 기본적인 개념과 파생되는 여러 용어들을 알아보자.

# 데이터베이스 기초 용어

만약 Relation, Domain, 복합 키(Composite key), 기본 키(Primary Key), RDBMS 등 데이터베이스 용어에 대해서 익숙하지 않다면 [이 링크](https://wkdtjsgur100.github.io/database-terms/)를 보고 오는 것을 추천한다.

# 인덱스란?

책을 보다 보면 맨 뒤쪽에 색인(Index) 페이지가 있어서 그 색인 페이지를 보고 원하는 본문의 내용을 찾아갔던 경험들은 한 번쯤 있을 것이다. 그 색인 페이지는 책의 공간을 일부 차지해서 책의 두께를 두껍게 한다는 단점이 있을 수는 있지만 원하는 본문의 내용을 빠르게 탐색하는데 큰 도움이 되기 때문에 주로 책의 용어들이 중요한 곳에서 많이 사용되고는 한다.  
데이터베이스의 인덱스는 이와 매우 유사하다. RDMS에서 실제 데이터 레코드를 더 빠르게 찾아가기 위해서, 데이터 레코드를 참조하는 인덱스를 만들어 탐색을 더 빠르게 한다. 책의 색인 페이지를 다시 예시로 들어보자. 색인 페이지는 주로 `단어 - 페이지 번호`로 구성이 되는데, RDBMS에서는 
