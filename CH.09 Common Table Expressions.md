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
  CTE_query_definition
  UNION [ALL]
  CTE_query definition
) SELECT * FROM cte_name;
```

