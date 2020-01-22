# Group By

GROUP BY절은 행을 SELECT 절에서 선택한 열(column)을 기준으로
묶을 수 있다. 각 그룹에 대해선 aggreate function(SUM, COUNT 와 같은 집계 함수)를 사용할 수 있다.
```
SELECT
  column_1,
  column_2,
  aggrgate_function(column_3)
FROM
  table_name
GROUP BY
  column_1,
  column_2
```
GROUP BY 절은 FROM, WHERE 다음 절로 나와야 한다.

* PostgreSQL GROUP BY 예

```
SELECT
   customer_id
FROM
   payment
GROUP BY # DISTINCT 와 같은 효과로 결과에서 중복 행을 제거
   customer_id;
```

```
SELECT
  customer_id,
  SUM (amount)
FROM
  payment
GROUP BY
  customer_id # SUM 그룹을 묶고
ORDER BY
  SUM (amount) DESC; # 내림차순으로 정렬
```

> 그룹(GROUP BY)을 필터링 할려면 WHERE가 아닌 HAVING 을 사용한다.

```
SELECT
  DATE(payment_date) paid_date,
  SUM(amount) sum
FROM
  payment
GROUP BY
  DATE(payment_date);
```
> 날짜별로 그룹화 하기 위해선 DATE() 함수르 사용하자.

## PostgreSQL HAVING

HAVING은 GROUP BY에 의해 만들어진 그룹을 필터링 하는 절이다.

```
SELECT
   column_1,
   aggregate_function (column_2)
FROM
   tbl_name
GROUP BY
   column_1
HAVING
   condition;
```
> PostgreSQL 에서는 GROUP BY 없이 HAVING 절 사용이 가능하다. 이 경우, HAVING 절은 쿼리를 단일 그룹으로 변환한다. 또한 SELECT 문 안에 
HAVING 절을 사용하고 싶을땐 집계 함수를 사용한 열만 가능하다.


* PostgreSQL HAVING 예제

```
SELECT
   customer_id,
   SUM (amount)
FROM
   payment
GROUP BY
   customer_id
HAVING
   SUM (amount) > 200; 
```
위에서 언급한 대로 SELECT 문에서 집계함수를 사용한 녀석들을 HAVING 절에서 조건으로 사용할 수 있다.

----


# PostgreSQL UNION

UNION 은 두개나 셋이상의 SELECT 문의 결과를 하나로 묶어 준다. 
```
SELECT
   column_1,
   column_2
FROM
   tbl_name_1
UNION
SELECT
   column_1,
   column_2
FROM
   tbl_name_2;
```
> 양 쿼리는 같은 칼럼의 숫자를 반환해야 한다.
> 쿼리의 해당 열에는 호환 가능한 데이터 타입을 갖고 있어야 한다.

UNION 은 UNION ALL 을 사용하지 않으면 중복된 rows는 삭제한다.

* PostgreSQL UNION 예시

<img src="https://www.postgresqltutorial.com/wp-content/uploads/2013/05/sales2007q1.png">

> sales 2007q1

<img src="https://www.postgresqltutorial.com/wp-content/uploads/2013/05/sales2007q2.png">

> sales 2007q2

```
SELECT *
FROM
   sales2007q1
UNION (ALL) # ALL을 사용하면 중복으로 삭제된 mary 열이 두번 반복된다.
SELECT *
FROM
   sales2007q2;
```

<img src="https://www.postgresqltutorial.com/wp-content/uploads/2013/05/PostgreSQL-UNION-ALL-result.png">

* PostgreSQL UNION with ORDER BY

```
SELECT *
FROM
   sales2007q1
UNION ALL
SELECT *
FROM
   sales2007q2
ORDER BY 
 name ASC,
 amount DESC;
```
UNION 을 사용하는 경우, 모든 쿼리의 맨 마지막부분에서 ORDER BY를 사용해야 예상한 결과가 나올 것이다.

## PostgreSQL INTERSECT Operator

PostgreSQL 에서는 UNION과 EXCEPT 처럼 INTERSECT 연산자는 두개 혹은 그 이상의 SELECT 문을 하나의 결과 셋으로 반환한다. INTERSECT는 
각 테이블의 공통된 부분(교집합)을 반환한다.

```
SELECT
  column_list
FROM
  A
INTERSECT
SELECT
  column_list
FROM
  B;
```

> 각 SELECT 절에서 사용하는 행(칼럼)의 수와 순서는 동일해야 한다.
> data types 은 호환 가능해야 한다.

```
SELECT
   employee_id
FROM
   keys
INTERSECT
SELECT
        employee_id
FROM
   hipos
ORDER BY employee_id; 
```
INTERSECT 의 결과를 정렬하기 위해 ORDER BY 절은 맨 마지막에 사용해야 한다.

## PostgreSQL EXCEPT Operator

PostgreSQL 에서는 UNION과 INTERSECT 처럼 EXCEPT 연산자는 두개 혹은 그 이상의 SELECT 문을 하나의 결과 셋으로 반환한다. 

메인테이블과 다른 테이블과 비교하여 공통인 부분(INTERSECT)을 제외한 메인테이블 부분만 반환한다.

```
SELECT column_list
FROM A
WHERE condition_a
EXCEPT
SELECT colum_list
FROM B
WHERE condition_b;
```

<img src="https://www.postgresqltutorial.com/wp-content/uploads/2016/06/PostgreSQL-EXCEPT.png">

> 각 SELECT 절에서 사용하는 열(칼럼)의 수와 순서는 동일해야 한다.
> data types 은 각각의 열(칼럼)에 호환 가능해야 한다.

* 예시

```
SELECT
  distinct inventory.film_id,
  title
FROM
  inventory
INNER JOIN film ON film.film_id = inventory.film_id
ORDER BY title;
```

인벤토리에 있는 영화들을 반환한다. 여기서 인벤토리에 있는 필름을 가져오기 위해선 어떻게 해야 할까?

```
SELECT
	film_id,
	title
FROM
	film
EXCEPT
	SELECT
		DISTINCT inventory.film_id,
		title
	FROM
		inventory
	INNER JOIN film ON film.film_id = inventory.film_id
ORDER BY title;
```

문장 끝에 ORDER BY 절을 배치해야 예상한대로 나오게 된다. EXCEPT 전, 즉 하나의 쿼리가 끝나는 순간 정렬을 하면 최종 결과에 정렬이 반영되지 않는다.

----

출처 : https://www.postgresqltutorial.com/
