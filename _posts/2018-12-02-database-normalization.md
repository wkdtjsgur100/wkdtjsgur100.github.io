---
layout: post
section-type: post
title: 데이터베이스 정규화 개념 설명 및 예제
category: database
tags: [ 'database' ]
comments: true
---

> 해당 글에서는 용어 정리 및 1~3 정규화(1~3NF)에 대해서 작성되어 있습니다. 

# 용어 정리

정규화에 대한 설명에 앞서 데이터베이스와 관련된 용어를 알아야 할 필요가 있다.(이를 기반으로 설명하기 때문) 따라서 아래 그림을 보고 도메인, 속성, 릴레이션, 튜플 등에 대한 용어를 숙지하자.  

![용어 정리](https://www.dropbox.com/s/7wuch5scloueeza/%EC%8A%A4%ED%81%AC%EB%A6%B0%EC%83%B7%202018-12-02%2017.16.29.png)  

- Relation(= Table): 관계형 데이터베이스(RDBMS)에서 정보를 구분하여 저장하는 기본 단위를 말한다. 예로 부서에 관한 정보를 저장하는 부서 Relation(혹은 부서 Table)이 있다.
- Domain(도메인): Relation에서 각각의 속성(Attribute)들이 취할 수 있는 같은 타입의 원자 값들의 집합.
- Atomic Value(원자 값): 더 이상 분리될 수 없는 값.

# 키(Key)

한 Relation에서 각각의 tuple(행)을 유일하게 식별하기 위해 사용하는 하나 혹은 그 이상의 속성들의 집합.

## 유일성과 최소성

- 유일성: 하나의 키로 어떠한 행을 바로 찾아낼 수 있는 성질. ex) 주민등록번호
- 최소성: 레코드를 식별하는데 꼭 필요한 속성들로만 구성되어 있는 성질. ex) {주민등록번호 + 학번}은 최소성을 만족시키지 않는다. 주민등록번호로만 tuple(행)을 구분할 수 있기 때문.

## 키의 종류

- 슈퍼 키(Super Key): **유일성을 만족하는 키.** 예를 들면, {학번 + 이름}, {주민등록번호 + 학번}
- 복합 키(Composite Key): **2개 이상의 속성(attribute)를 사용한 키.**
- 후보 키(Candidate key): **유일성과 최소성을 만족하는 키.** 기본키가 될 수 있는 후보이기 때문에 후보키라고 불린다. 예를 들면, 주민등록번호, 학번 등
- 기본 키(Primary key): **후보 키에서 선택된 키.** NULL값이 들어갈 수 없으며, 기본키로 선택된 속성(Attribute)은 동일한 값이 들어갈 수가 없다.
- 대체 키(Surrogate key): **후보 키 중에 기본 키로 선택되지 않은 키.**

아래는 이해를 돕기 위한 그림이다.
![키 개념](https://www.dropbox.com/s/l51vuclpeqkdmwr/%EC%8A%A4%ED%81%AC%EB%A6%B0%EC%83%B7%202018-12-04%2014.33.19.png)

# 데이터베이스 정규화

데이터베이스 정규화란 데이터의 중복을 줄이고 무결성을 향상 시키는 등 여러 목적을 달성하기 위해 관계형 데이터베이스를 정규화된 형태로 재디자인하는 것을 말한다.

## 정규화의 목적

- 불필요한 데이터를 제거, 데이터의 중복을 최소화 하기 위해서
- 데이터베이스 구조 확장 시 재디자인을 최소화
- 다양한 관점에서의 query를 지원하기 위해서
- 각종 이상현상(삽입, 갱신, 삭제 이상)을 방지하기 위해서, 테이블의 구성을 논리적이고 직관적으로 한다.

## 정규화의 대상

온라인 거래 시스템 같은 OLTP(OnLine Transaction Processing) 데이터베이스는 CRUD(Create, Read, Update, Delete) 가 많이 일어나기 때문에 정규화 되는것이 좋지만, 분석 리포트 같은 OLAP(OnLine Analytical Processing) 데이터베이스는 분석과 리포팅을 위해 사용되기 때문에 연산의 속도를 위해 반정규화(denormalization)의 대상이 된다.

> 반정규화(denormalization)란, 정규화된 시스템을 성능 향상 및 개발과 운영의 단순화를 위해 역으로 정규화를 수행하는 것을 말한다. 일반적으로 join을 많이 사용해야 할 경우, 대량의 범위를 자주 처리하는 경우 등 조회에 대한 처리가 중요하다고 판단될 때 부분적으로 반정규화를 한다.

## 정규화의 과정

정규화는 1정규화 ~ 6정규화 까지 있지만, 실무에서는 대체로 1~3 정규화까지의 과정을 거친다.

## 제 1정규화(First Normal Form, 1NF)

테이블(Relation)이 제 1정규형을 만족했다는 것은 아래 세 가지 조건를 만족했다는 것을 의미한다.

1. 어떤 Relation에 속한 모든 Domain이 원자값(atomic value)만으로 되어 있다.
2. 반복되는 그룹(repeating group)이 없다.
3. 기본키를 사용하여 관련 데이터의 각 집합을 고유하게 식별할 수 있어야 한다.

예제를 통해 보자. 아래는 제 1정규형의 위 세가지 조건 중 첫번째 조건를 위반한 사례이다.  

![1NF 위반 사례1](https://www.dropbox.com/s/9s8vowdzs3t66uw/%EC%8A%A4%ED%81%AC%EB%A6%B0%EC%83%B7%202018-12-02%2017.50.02.png)  

위의 그림을 보면 전화번호가 여러개를 가지고 있기 때문에 이는 atomic하지 않게 되고, 이는 1 정규형을 위반한 것이 된다.

![1NF 위반 사례2](https://www.dropbox.com/s/rk4jovticy5y3fw/%EC%8A%A4%ED%81%AC%EB%A6%B0%EC%83%B7%202018-12-02%2017.54.10.png)  

위의 그림은 전화번호 그룹이 반복되는 경우이다.(2번 조건를 위반한 사례)  
따라서 이를 재디자인 할 필요가 있다. 아래는 제 1정규화를 만족시키기 위해 바꾼 디자인이다.  

![1NF 재디자인1](https://www.dropbox.com/s/1rr8ofxuy46i61b/%EC%8A%A4%ED%81%AC%EB%A6%B0%EC%83%B7%202018-12-02%2018.00.52.png)  

위의 Relation은 ID가 더 이상 고유하게 식별할 수 있는 키가 아니란 것을 확인할 수 있다. 따라서, 3번 조건을 만족하기 위해 아래와 같이 테이블을 나누어 디자인 하는 것이 좋다.  

![1NF 재디자인2](https://www.dropbox.com/s/dpuppv89n42ubre/%EC%8A%A4%ED%81%AC%EB%A6%B0%EC%83%B7%202018-12-02%2022.55.29.png)  

위의 디자인은 Customer Name과 Customer Telephone Number가 One-to-Many의 관계를 형성하는 것을 알 수 있다.

## 제 2정규화(Second Normal Form, 2NF)

제 2정규화를 수행 했을 경우 테이블의 **모든 컬럼이 완전 함수적 종속을 만족한다.**(부분 함수적 종속을 모두 제거되었다.) 이를 이해하기 위해서는 부분 함수적 종속과 완전 함수적 종속이라는 용어를 알아야 한다.

- 함수적 종속: X의 값에 따라 Y값이 결정될 때 X -> Y로 표현하는데, 이를 Y는 X에 대해 **함수적 종속** 이라고 한다. 예를 들어 학번을 알면 이름을 알 수 있는데, 이 경우엔 학번이 X가 되고 이름이 Y가 된다. X를 결정자이라고 하고, Y는 종속자라고 한다. 다른 말로 X가 바뀌었을 경우 Y가 바뀌어야만 한다는 것을 의미한다. 
- 함수적 종속에서 X의 값이 여러 요소일 경우, 즉, {X1, X2} -> Y일 경우, X1와 X2가 Y의 값을 결정할 때 이를 **완전 함수적 종속** 이라고 하고, X1, X2 중 하나만 Y의 값을 결정할 때 이를 **부분 함수적 종속** 이라고 한다.  

예시를 들어 살펴보자.  
![2NF 위반 사례](https://www.dropbox.com/s/c2xfxdanbuiaw1l/%EC%8A%A4%ED%81%AC%EB%A6%B0%EC%83%B7%202018-12-03%2006.58.17.png)  

위에서 Model과 Manufacturer를 알면 Model Full Name 필드를 아예 유지하지 않거나 참조하지 않아도 결정되기 때문에, {Model, Manufacturer} -> Model Full Name 이라고 할 수 있다. 
하지만 {Model, Manufacturer} -> Manufacturer Country에서  Model과 Manufacturer Country는 아무런 연관 관계가 없기 때문에, Manufacturer Country는 Manufacturer와만 종속관계에 있게 되고 이를 부분 함수 종속이라고 하게 되는 것이다. 아래의 그림을 보면 쉽게 이해할 수 있다.

![부분 함수 종속](https://www.dropbox.com/s/q4qcy99obhv6vnu/%EC%8A%A4%ED%81%AC%EB%A6%B0%EC%83%B7%202018-12-03%2010.49.33.png)  

위에서 부분 함수 종속을 제거 하게 되면, 아래와 같은 그림이 된다.  
![부분 함수 종속 제거 후](https://www.dropbox.com/s/v88uavl6zs5jsiz/%EC%8A%A4%ED%81%AC%EB%A6%B0%EC%83%B7%202018-12-03%2010.53.55.png)  

따라서, 부분 함수 종속을 제거한 이후의 테이블은 아래와 같고, 이는 제 2정규형을 만족한 테이블이다.  
![부분 함수 종속 제거 후 테이블](https://www.dropbox.com/s/x8481598dhnpzeg/%EC%8A%A4%ED%81%AC%EB%A6%B0%EC%83%B7%202018-12-03%2010.58.15.png)

## 제 3정규화(Third Normal Form, 3NF)

테이블(Relation)이 제 3정규형을 만족한다는 것은 아래 두 가지 조건을 만족하는 것을 의미한다.
1. Relation이 제 2정규화 되었다.(The relation is in second normal form)
2. 기본 키(primary key)가 아닌 속성(Attribute)들은 기본 키에만 의존해야 한다.

아래는 두 번째 조건이 위반된 사례이다.  
![3NF 위반 사례](https://www.dropbox.com/s/xtfoetv8hg6jn3f/%EC%8A%A4%ED%81%AC%EB%A6%B0%EC%83%B7%202018-12-03%2012.59.46.png)  

위 테이블에서 {Tournament, Year}가 후보키가 된다. 하지만 Winner Date of Birth은 기본키가 아닌 속성인 Winner를 거쳐 {Tournament, Year}에 의존하고 있는 것을 알 수 있는데, 이는 3NF를 위반한 것이 된다. 따라서 테이블을 아래와 같이 둘로 나누어 주자.  

![3NF 충족](https://www.dropbox.com/s/ks03nkc26nsffin/%EC%8A%A4%ED%81%AC%EB%A6%B0%EC%83%B7%202018-12-04%2014.51.39.png)  

위 테이블은 3NF를 만족한 상태이다.

# 출처

- Wikipedia First Normal Form(https://en.wikipedia.org/wiki/First_normal_form)
- Wikipedia Second Normal Form(https://en.wikipedia.org/wiki/Second_normal_form)
- Wikipedia Third Normal Form(https://en.wikipedia.org/wiki/Third_normal_form)
- 용어정리(http://dev-ahn.tistory.com/66)
