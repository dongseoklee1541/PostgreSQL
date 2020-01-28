# PostgreSQL CTE(Common Table Expression)

CTE는 SELECT, INSERT, UPDATE or DELETE등 을 포함하는 SQL을 참조 할 수 있는 임시 결과 집합이다. 
CTE는 쿼리를 실행하는 동안만 존재하는 일시적인 특징이 있다. 

```
WTIH cte_name (column_list) AS (
  CTE_query_definition
)
statment;
```

> 먼저 옵션 열 리스트의 이름을 특정한 뒤 두번째로 WTIH절 안에서 결과 값을 받을 열을 지정한다. 만약 열 리스트를 명시적으로 표기하지 않는다면,
CTE_query_definition이 CTE의 공통 리스트가 된다.
> 세번째로 CTE를 SELECT, INSERT, UPDATE or DELETE 처럼 statement를 뷰 또는 테이블로 사용하라.

CTE는 **joins** 이나 **subqueries** 로 사용된다.

* 예시

```
# CTE를 cte_film 으로 정의
WITH cte_film AS (
	SELECT
		film_id,
		title,
		(CASE
			WHEN length < 30 THEN 'Short'
			WHEN length >= 30 AND length < 90 THEN 'Medium'
			WHEN length > 90 THEN 'Long'
		END) length
	FROM
		film
)
# SELECT 문을 사용해서 행으로 채우는 문
SELECT
	film_id,
	title,
	length
FROM
	cte_film
WHERE
	length = 'Long'
ORDER BY
	title;
```

CTE인 cte_film 을 SELECT문에서 사용하고, length='Long' 인 값들만 반환하는 쿼리문이다.

* Joining CTE with table

```
WITH cte_rental AS (
	SELECT staff_id,
		COUNT(rental_id) rental_count
	FROM rental
	GROUP BY staff_id
)
SELECT s.staff_id,
	first_name,
	last_name,
	rental_count
FROM staff s
	INNER JOIN cte_rental USING (staff_id);
```

우선 WTIH 문 내부에서 CTE을 정의하고, 기존에 존재하는 table과 join하는데 사용한다.

<img src="https://sp.postgresqltutorial.com/wp-content/uploads/2019/01/PostgreSQL-CTE-joined-with-a-table.png">

### CTE 사용의 이점

> 복잡한 쿼리에서 가독성을 증가 시킬 수 있다. CTE를 사용해 복잡한 쿼리를 보다 체계적이고 더 읽기 편한 방법을 만들 수 있다.

> 재귀 쿼리를 만들 수 있다. 재귀 쿼리는 자신을 참조하는 쿼리다. 재귀 쿼리는 데이터의 조직도 또는 bill of materials 을 할때 유용하다.

> window functions과 함께 사용한다. CTE를 window functions과 함께 사용해 초기 결과 세트를 생성하고 다른 선택 문을 사용해 이 결과를 사용할 수 있다.

## Learn PostgreSQL Recursive Query By Example

재귀 쿼리는 자신을 참조하는 쿼리다. 재귀 쿼리는 데이터의 조직도 또는 bill of materials 을 할때 유용하다.

```
WITH RECUSERIVE cte_name(
  CTE_query_definition --- non recursive term
  UNION [ALL]
  CTE_query definition --- recursive term
) SELECT * FROM cte_name;
```

재귀 CTE는 다음과 같이 세가지 요소가 있다.

> non-recursive term : CTE 구조의 결과 집합에 대한 정의다.

> Recursive term : non-recursive term 과 JOIN 또는 UNION (ALL)을 활용해 연결되어 사용되는 녀석, Recursive term은 CTE 이름 자체를 참조한다.

> 종료 체크(terminal check) : 이전의 반복(previous itertation)에서 행이 반환되지 않으면 재귀는 멈춘다.


PostgreSQL 은 다음과 같은 방식으로 재귀를 실행한다.

> 첫째, 재귀가 아닌 항(non-recursive term)에서 기준 결과를 만든다.(R0)
> 둘째, 재귀 항(recursive term)을 Ri를 인풋값으로 반복하고, 결과 집합을 Ri + 1 출력으로 보낸다.
> 셋째, 반환 값이 없을때까지 두번째를 반복한다.(terminal check)
> 결과 집합(R0, R1, ..., Rn)의 UNION or UNION ALL을 한 것을 최종 결과 값으로 반환한다.

* 재귀 쿼리 예시

```
WITH RECURSIVE subordinates AS (
   SELECT
      employee_id,
      manager_id,
      full_name
   FROM
      employees
   WHERE
      employee_id = 2
   UNION
      SELECT
         e.employee_id,
         e.manager_id,
         e.full_name
      FROM
         employees e
      INNER JOIN subordinates s ON s.employee_id = e.manager_id
) SELECT
   *
FROM
   subordinates;
```

UNION 위의 절에서 subordinates 를 정의하는 non-recursive term을 시작한다. 이후 재귀를 하는 테이블인 employees를 통해서 UNION 이하 절에서 반복한다. 언제까지? 반환되는 rows가 없을때까지.

```
 employee_id | manager_id |    full_name
-------------+------------+-----------------
           2 |          1 | Megan Berry
           6 |          2 | Bella Tucker
           7 |          2 | Ryan Metcalfe
           8 |          2 | Max Mills
           9 |          2 | Benjamin Glover
          16 |          7 | Piers Paige
          17 |          7 | Ryan Henderson
          18 |          8 | Frank Tucker
          19 |          8 | Nathan Ferguson
          20 |          8 | Kevin Rampling
(10 rows)
```
첫번째 subordinates 였던 eployee_id 가 2인 경우 6,7,8,9를 반환시켜줬고 반환된 숫자를 통해서 16,17,18,19,20을 반환할 수 있었다. 하지만 이번에 반환된 숫자들 중 manager_id에 포함된 경우가 없어서 재귀는 종료된다.

