# PostgreSQL GROUPING SETS

GROUPING SETS은 사용자가 그룹화 할 수 있는 열 세트다. 일반적으로 단일 집계 쿼리(single aggregate query)는 단일 그룹화 세트를 정의한다.

```
SELECT
  segement,
  SUM (quantity)
FROM
  sales
```
```
SELECT
    brand,
    segment,
    SUM (quantity)
FROM
    sales
GROUP BY
    brand,
    segment
 
UNION ALL
 
SELECT
    brand,
    NULL,
    SUM (quantity)
FROM
    sales
GROUP BY
    brand
 
UNION ALL
 
SELECT
    NULL,
    segment,
    SUM (quantity)
FROM
    sales
GROUP BY
    segment
 
UNION ALL
 
SELECT
    NULL,
    NULL,
    SUM (quantity)
FROM
    sales;
```
모든 그룹화 세트를 한번에 보기 위해선 위와 같이 UNION ALL을 사용하면 되지만, 코드가 너무 길고 속도가 느린 단점이 있다.

더 효율성을 높이기 위한 방법으로 GROUP BY 절의 하위 절인 GROUPING SETS이다.

GROUPING SETS에서 동일한 쿼리에 여러 그룹화 집합을 정의할 수 있다.

```
SELECT
	c1,
	c2,
	aggreate_function(c3)
FROM
	table_name
GROUP BY
	GROUPING SETS(
	(c1,c2),
	(c1),
	(c2),
	()
);
```
일반적으로 위와 같이 사용하며, 그룹은 (c1,c2),(c1),(c2),() 4개의 세트다.

* UNION ALL 대신 GROUPING SETS을 사용한 케이스

```
SELECT
    brand,
    segment,
    SUM (quantity)
FROM
    sales
GROUP BY
    GROUPING SETS (
        (brand, segment),
        (brand),
        (segment),
        ()
    );
```

UNION ALL 에 비해 훨씬 짧고 가독성과 효율성이 뛰어난 방식이다.

### Grouping function

GROUPING 함수는 열의 이름을 받아서 grouping sets의 멤버라면 0을 반환하고, 아니라면 1을 반환한다.

```
SELECT
   GROUPING(brand) grouping_brand,
   GROUPING(segment) grouping_segement,
   brand,
   segment,
   SUM (quantity)
FROM
   sales
GROUP BY
   GROUPING SETS (
      (brand, segment),
      (brand),
      (segment),
      ()
   )
ORDER BY
   brand,
   segment;
```

<img src="https://www.postgresqltutorial.com/wp-content/uploads/2018/03/PostgreSQL-GROUPING-SETS-GROUPING-function.png">

## PostgreSQL CUBE

PostgreSQL CUBE는 GROUP BY 절의 하위절로 CUBE는 여러 그룹화 집합(grouping sets) 세트를 만들 수 있다. grouping sets은 그룹화할 열들의 집합이다.

```
SELECT
  c1,
  c2,
  c3,
  aggreate(c4)
FROM
  table_name
GROUP BY
  CUBE(c1,c2,c3);
```

> CUBE는 GROPU BY의 하위 절로 쓴다.
> select 목록에서 분석하고 집계할 함수식을 지정한다.
> 마지막으로 GROUP BY 열에서 CUBE괄호 안에 사용할 열의 차원을 선택해라.

일반적으로 CUBE 괄호 안에 들어간 열의 갯수가 n이면 2^n 개의 조합을 얻을 것이다.

* 예시

<img src="https://www.postgresqltutorial.com/wp-content/uploads/2018/03/sales-table.png">

```
SELECT
    brand,
    segment,
    SUM (quantity)
FROM
    sales
GROUP BY
    CUBE (brand, segment)
ORDER BY
    brand,
    segment;
```

<img src="https://www.postgresqltutorial.com/wp-content/uploads/2018/03/PostgreSQL-CUBE-example.png">

다음 쿼리는 부분 큐브를 실행한다.

```
SELECT
    brand,
    segment,
    SUM (quantity)
FROM
    sales
GROUP BY
    brand,
    CUBE (segment)
ORDER BY
    brand,
    segment;
```

<img src="https://www.postgresqltutorial.com/wp-content/uploads/2018/03/PostgreSQL-CUBE-partial-cube-example.png">

