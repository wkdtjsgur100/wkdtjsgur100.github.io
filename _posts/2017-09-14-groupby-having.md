---
layout: post
section-type: post
title: (MySQL) GROUP BY와 HAVING
category: mysql
tags: [ 'mysql' ]
---

GROUP BY와 HAVING은 다음과 같은 syntax를 따릅니다.

``` sql
SELECT column-names
  FROM table-name
 WHERE condition
 GROUP BY column-names
HAVING condition
```

# GROUP BY

MySQL에서의 GROUP BY는 특정 컬럼 이름을 지정(column-names)해주면 그 컬럼의 **UNIQUE한 값에 따라서 데이터를 그룹 짓고, 중복된 열은 제거됩니다.**    
GROUP BY는 보통 집합 함수(*aggregate function*, [AVG, SUM, COUNT 등을 말합니다])와 같이 쓰이며, 다음과 같은 형태를 지닙니다.

``` sql
SELECT
    c1, c2,..., cn, aggregate_function(ci)
FROM
    table
WHERE
    where_conditions
GROUP BY c1 , c2,...,cn;
```

예를 들어, 다음과 같은 order 테이블이 있다고 합시다.  

![order table](/images/posts/order_table.png)

아래의 SQL 구문을 실행한 결과(status 별로 그룹지어서 각각의 갯수를 센 결과로 테이블을 만듭니다),

``` sql
SELECT
    status, COUNT(*)
FROM
    orders
GROUP BY status;
```

결과는 다음과 같습니다.  

![groupby order table](/images/posts/groupby_order_table.png)

# HAVING

HAVING은 간단하게 생각해서 GROUP BY한 결과에 조건을 붙이고 싶을때, 즉 GROUP BY의 WHERE 절과도 같다고 볼 수 있습니다.  

위의 예제에서, 각각 status의 총 갯수(COUNT)가 4이상인 경우를 예제로 보여드리겠습니다.

``` sql
SELECT
    status, COUNT(*)
FROM
    orders
GROUP BY status
HAVING COUNT(*) > 4;
```

결과는 다음과 같습니다.  

![order having](/images/posts/order_having.png)
