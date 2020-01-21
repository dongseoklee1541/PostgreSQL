# PostgreSQL Joins

<img src="https://theartofpostgresql.com/img/SQL-JOINS-Example-0.png">

Join은 테이블 간에 일반 열 값에 따라 하나 이상의 테이블에서 열을 결합하는데 사용된다. 일반적으로 공통 열은 첫번째 테이블은 Primary Key(기본 키)
열 및 두번째 테이블의 Foreign Key(외부 키)열이다.
> PostgreSQL에서 지원하는 Joins은 inner,left,right,full outer, cross, natural, self-join 이 있다. 

```
SELECT
    a.id id_a,
    a.fruit fruit_a,
    b.id id_b,
    b.fruit fruit_b
FROM
    basket_a a
INNER(LEFT, OUTER,  JOIN basket_b b ON a.fruit = b.fruit;
```

----

출처 : https://theartofpostgresql.com/blog/2019-09-sql-joins/
