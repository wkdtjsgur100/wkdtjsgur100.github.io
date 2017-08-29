---
layout: post
section-type: post
title: 기초 MySQL 튜토리얼(공식 사이트 번역)(1)
category: mysql
tags: [ 'mysql' ]
---

> MySQL tutorial 공식 홈페이지에 있는 문서 [Basic MySQL Tutorial](http://www.mysqltutorial.org/basic-mysql-tutorial.aspx)을 번역했습니다.

# 기초 MySQL 튜토리얼

여기에선 몇몇 기초적인 SQL 상태(statements)에 대해 나와있습니다. 관계형 데이터베이스 관리 시스템을 처음으로 사용하려는 거라면 이 튜토리얼은 데이터 쿼리, 데이터 업데이트, 데이터베이스 관리, 테이블 생성 같은 MySQL에서 하는 일을 전부 알려줄 것입니다.  

Oracle, Microsoft SQL Server, PostgreSQL 같은 다른 관계형 데이터 베이스를 이미 했었다면, 원래 지식들을 상기시키거나 다른 시스템들과 비교에서 MySQL은 어떻게 SQL문을 실행하는지에 대해서 알수 있을 것입니다.  

## Section 1. MySQL 시작하기

일단 MySQL을 설치하고, 샘플 Database를 다운로드 하고, 실습으로 MySQL 서버에 데이터를 로딩하는 것으로 시작하겠습니다.
- [MySQL 데이터베이스 서버 설치하기](http://www.mysqltutorial.org/install-mysql/) - 컴퓨터에 MySQL 데이터베이스 서버를 어떻게 설치하는지 하나하나 보여줍니다.
- [MySQL 샘플 데이터베이스 다운로드](http://www.mysqltutorial.org/mysql-sample-database.aspx) - classicmodels 라고 이름 지어진 MySQL 샘플 데이터베이스를 소개하고, 다이어그램과 샘플 데이터베이스를 다운로드 할 수 있는 링크를 제공합니다.
- [자신만의 MySQL 데이터베이스 서버에 샘플 데이터베이스 로딩하기](http://www.mysqltutorial.org/how-to-load-sample-database-into-mysql-database-server.aspx) - 실습을 통해 MySQL 데이터베이스 서버에 classicmodels 샘플 데이터베이스를 어떻게 로딩하는지 차근히 알아볼 수 있습니다.

## Section 2. 데이터 쿼리

이 섹션은 MySQL 데이터베이스 서버로 부터 데이터 쿼리를 어떻게 하는지 설명합니다. 싱글 테이블에서 데이터를 쿼리하기 위해 사용되는 간단한 `SELECT` 상태문으로 시작하겠습니다.

- [SELECT](http://www.mysqltutorial.org/mysql-select-statement-query-data.aspx) - 싱글 테이블로 부터 데이터를 쿼리하는 `SELECT`문을 사용하는 방법에 대해 보여줍니다.
- [SELECT DISTINCT](http://www.mysqltutorial.org/mysql-distinct.aspx) - 결과셋에서 중복된 열을 제거하는 `SELECT` 문의 `DISTINCT` 연산자를 사용하는 방법에 대해 배워봅니다.

## Section 3. 데이터 필터링

- [WHERE](http://www.mysqltutorial.org/mysql-where/) - 특정 상태를 기반으로 한 열을 필터링하기 위해 `WHERE` 구문을 사용하는 방법을 알아봅시다.
- [AND](http://www.mysqltutorial.org/mysql-and/) - 데이터 필터링을 위해 복잡한 상태를 형상화하기 위한 Boolean 표현을 조합하는 `AND` 연산자를 소개합니다.
- [OR](http://www.mysqltutorial.org/mysql-or/) - 데이터 필터링을 위한 `AND` 연산자로 `OR` 연산자를 조합하는 방법에 대해 보여주고 설명합니다.
- [IN](http://www.mysqltutorial.org/sql-in.aspx) - 한 서브쿼리나 한 리스트에 있는 어떤 값을 어떤 한 값과 매칭되는 지를 결정하는 `WHERE` 구문의 `IN` 연산자를 사용하는 방법에 대해 보여줍니다.
- [BETWEEN](http://www.mysqltutorial.org/mysql-between) - `BETWEEN` 연산자를 사용해서 한 범위를 기반으로한 데이터 쿼리를 어떻게 하는지 보여줍니다.
- [LIKE](http://www.mysqltutorial.org/mysql-like/) - 특정 패턴을 기반으로 한 데이터 쿼리를 위한 기술들을 설명합니다.
- [LIMIT](http://www.mysqltutorial.org/mysql-limit.aspx) - `SELECT`문에 의해서 반환된 열의 갯수를 제한하기 위해 `LIMIT`을 사용합니다.
- [IS NULL](http://www.mysqltutorial.org/mysql-is-null/) - `IS NULL` 연산자를 사용해서 값이 NULL인지 아닌지 테스트 합니다.

## Section 4. 데이터 정렬

- [ORDER BY](http://www.mysqltutorial.org/mysql-order-by/) - `ORDER BY` 문을 사용해서 결과셋을 정렬하는 방법에 대해서 나와있습니다. `FIELD` 함수로 커스텀 정렬을 하는것도 포함되어있습니다.
- [`ORDER BY`문을 사용한 자연스러운 정렬](http://www.mysqltutorial.org/mysql-natural-sorting/) - `ORDER BY`문을 사용해서 MySQL을 다양하고 자연스레 정렬하는 방법에 대해 나와있습니다.

## Section 5. 테이블 조인

- [MySQL alias](http://www.mysqltutorial.org/mysql-alias/) - 복잡한 쿼리문의 가독성을 증가시키기 위한 테이블 alias와 aliases에 대해 설명합니다.
- [MySQL Join](http://www.mysqltutorial.org/mysql-join/) - cross join. inner join, left join, right join가 포함된 MySQL에서 지원하는 join들에 대한 오버뷰가 제공됩니다.
- [INNER JOIN](http://www.mysqltutorial.org/mysql-inner-join.aspx) - 관련된 여러개의 테이블로부터 데이터를 쿼리하는 inner join을 사용합니다.
- [LEFT JOIN](http://www.mysqltutorial.org/mysql-left-join.aspx) - 다양한 context들에서 left join 구문을 적용하는 방법에 대해서 배웁니다
- [RIGHT JOIN](http://www.mysqltutorial.org/mysql-right-join/) - 두개 혹은 그 이상의 테이블로부터 데이터를 쿼리하기 위해 right join을 사용하는 방법에 대해 나와있습니다.
- [CROSS JOIN](http://www.mysqltutorial.org/mysql-cross-join/) - 다중 테이블로부터 열들의 데카르트 곱을 만듭니다.
- [Self-join](http://www.mysqltutorial.org/mysql-self-join/) - 자체적으로 테이블 alias를 사용하는 테이블을 join하고, left join과 inner join을 포함한 다른 종류의 join들을 사용하는 바로 그 테이블 안의 열들을 연결합니다.

## Section 6. 데이터 그룹

- [GROUP BY clause](http://www.mysqltutorial.org/mysql-group-by.aspx) - 행이나 표현들을 기반으로한 그룹들의 열을 어떻게 그룹짓는지에 대해 보여줍니다.
- [HAVING](http://www.mysqltutorial.org/mysql-having.aspx) - 특정 상태에 따른 그룹들을 필터링 합니다.

## Section 7. MySQL 서브쿼리, 파생테이블, CTE(Common Table Expression)

- [MySQL 서브쿼리](http://www.mysqltutorial.org/mysql-subquery/) - 또 다른 쿼리(외부 쿼리) 안에 query를 안전하게 넣는 방법에 대해서 보여줍니다.
- [MySQL 파생테이블](http://www.mysqltutorial.org/mysql-derived-table/) - 파생 테이블 개념에 관해서 소개하고, 복잡한 쿼리들을 간소화시키기 위해서 이를 어떻게 사용하는지에 대해서 나와있습니다.
- [MySQL CTE](http://www.mysqltutorial.org/mysql-cte/) - CTE 개념에 대해서 설명하고 테이블로부터 데이타 쿼리하는 CTE를 사용하는 방법에 대해서 보여줍니다
- [재귀 CTE](http://www.mysqltutorial.org/mysql-recursive-cte/) - 계층 데이터를 가로지르는 재귀 CTE를 사용하는 방법에 대해서 안내합니다.

## Section 8. Set 연산자들의 사용

- [UNION and UNION ALL](http://www.mysqltutorial.org/sql-union-mysql.aspx) - 실글 결과셋에 여러 쿼리의 결과셋을 결합하는 것입니다.
- [INTERSECT](http://www.mysqltutorial.org/mysql-intersect/) - MySQL에서 `INTERSECT` 연산자를 simulate하는 두 방법을 보여줍니다.
- [MINUS](http://www.mysqltutorial.org/mysql-minus/) - SQL MINUS 연산자에 대해서 설명하고 MySQL에서 이를 어떻게 simulate하는지 보여줍니다.
