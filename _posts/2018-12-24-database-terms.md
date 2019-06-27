---
layout: post
section-type: post
title: 데이터베이스 용어 및 기초 개념 정리(DBMS, Key관련 용어, 테이블 관련 용어 등)
category: database
tags: [ 'database' ]
comments: true
---

> 데이터베이스의 여러 개념들을 이해하기 전 여러 용어들을 숙지해야 한다.

# 기초 용어

- 엔티티(Entity): **사람, 장소, 사물, 사건 등과 같이 독립적으로 존재하면서 고유하게 식별이 가능한 실세계의 객체이다.** 예를 들어, 사원번호가 2106이고 이름이 김창섭인 사원, 과목 코드가 CS372인 운영체제 등이 있다. 이렇게 실체가 있는 것 뿐만 아니라 추상적인 것도 있다. 프로젝트 번호가 P101인 프로젝트, 학과 번호가 D315인 학과는 추상적인 개념의 엔티티가 될 수 있다. 엔티티는 엔티티 집합들로 분류된다.
- 엔티티 집합(Entity Set): 동일한 속성(attribute)를 가진 엔티티들의 집합이다. 예를 들어, 한 회사의 모든 사원들은 EMPLOYEE라는 엔티티 집합을 이루고, 모든 부서들은 DEPARTMENT 엔티티 집합에 속한다. 어떤 사원은 EMPLOYEE와 MANAGER 라는 두 엔티티 집합에 속할 수 있기 때문에, 엔티티 집합에 속한 요소들이 항상 서로 다르지는 않다. 엔티티 타입(Entity Type)이라고도 한다.
- 스키마(Schema): 데이터를 설명하는 데이터. 메타데이터(Metadata)라고도 한다. 예를 들어 `홍길동, 1993-04-02`이라는 데이터가 있을 경우에 이는 `이름, 생일` 이라는 스키마로 설명할 수 있다.

# 테이블(Relation) 관련 용어

![용어 정리](http://dl.dropbox.com/s/7wuch5scloueeza/%EC%8A%A4%ED%81%AC%EB%A6%B0%EC%83%B7%202018-12-02%2017.16.29.png)  
그림 출처: http://dev-ahn.tistory.com/66  

- Relation(= Table): 관계형 데이터베이스(RDBMS)에서 정보를 구분하여 저장하는 기본 단위를 말한다. 예로 부서에 관한 정보를 저장하는 부서 Relation(혹은 부서 Table)이 있다.
- Domain(도메인): Relation에서 각각의 속성(Attribute)들이 취할 수 있는 같은 타입의 원자 값들의 집합. 즉, 한 속성(Attribute)에 나타날 수 있는 값의 범위. 예를 들면 학년 속성은 1~6까지의 값을 가질 수 있고 이를 도메인이라 한다.
- Atomic Value(원자 값): 더 이상 분리될 수 없는 값.

# Key

- 키(Key): 한 Relation에서 각각의 tuple(행)을 유일하게 식별하기 위해 사용하는 하나 혹은 그 이상의 속성들의 집합.

## 유일성과 최소성

- 유일성: 하나의 키로 어떠한 행을 바로 찾아낼 수 있는 성질. ex) 주민등록번호
- 최소성: 레코드를 식별하는데 꼭 필요한 속성들로만 구성되어 있는 성질. ex) {주민등록번호 + 학번}은 최소성을 만족시키지 않는다. 주민등록번호로만 tuple(행)을 구분할 수 있기 때문.

## 키의 종류

- 슈퍼 키(Super Key): **유일성을 만족하는 키.** 예를 들면, {학번 + 이름}, {주민등록번호 + 학번}
- 복합 키(Composite Key): **2개 이상의 속성(attribute)를 사용한 키.**
- 후보 키(Candidate key): **유일성과 최소성을 만족하는 키.** 기본키가 될 수 있는 후보이기 때문에 후보키라고 불린다. 예를 들면, 주민등록번호, 학번 등
- 기본 키(Primary key): **후보 키에서 선택된 키.** NULL값이 들어갈 수 없으며, 기본키로 선택된 속성(Attribute)은 동일한 값이 들어갈 수가 없다.
- 대체 키(Surrogate key): **후보 키 중에 기본 키로 선택되지 않은 키.**
- 외래 키(Foreign Key): **어떤 테이블(Relation) 간의 기본 키(Primary key)를 참조하는 속성이다.** 테이블(Relation)들 간의 관계를 나타내기 위해서 사용된다.

아래는 이해를 돕기 위한 그림이다.
![키 개념](http://dl.dropbox.com/s/l51vuclpeqkdmwr/%EC%8A%A4%ED%81%AC%EB%A6%B0%EC%83%B7%202018-12-04%2014.33.19.png)

# DBMS(Database Management System)

데이터베이스를 관리하는 시스템은 여러 종류가 있다. 예전에 쓰이던 시스템에서 최근까지 쓰이는 DBMS에 대해 알아보자. 아래에서 RDBMS, No-SQL이 최근 대부분의 DBMS를 차지하고 있다.

- HDBMS(Hierachical DBMS): 계층적인 형태의 DBMS. 초기 세팅이 변화하면 이에 대처하기가 힘들어 잘 쓰이지 않음. 초기 DBMS의 형태.
- NDBMS(Network DBMS): 구성과 설계가 복잡하고 데이터 종속성을 해결하지 못함.
- RDBMS(Relational DBMS, 관계형 데이터베이스 관리 시스템): 테이블과 테이블의 관계를 기반으로 하는 가장 범용적인 데이터베이스 관리 시스템. 데이터의 일관성(Consistency)를 보장함. MySQL, Oracle, SQLite 등이 있음.
- ODBMS(Object-Oriented DBMS): 정보를 객체의 형태로 표현하는 DBMS.
- No-SQL(Not-Only SQL): 데이터 간의 관계를 설정하지 않고 유연한 테이블 스키마를 가진다. 대용량 데이터/분산 처리에 적합하다는 장점이 있지만 데이터 일관성(Consistency)이 항상 보장되지 않는 단점이 있다. MongoDB, HBase, Cassandra, Redis 등이 있다.

# 참고 서적

- 데이터베이스 배움터
