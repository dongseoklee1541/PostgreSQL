# PostgreSQL Subquery

Subquery는 다른 쿼리안에 중첩된 쿼리다. 하위 쿼리를 사용하기 위해선, 어느 절에서 사용하던 괄호 안에 넣고 사용해야 한다.

```
SELECT
	film_id,
	title,
	rental_rate
FROM
	film
WHERE
	rental_rate > (
		SELECT 
			AVG (rental_rate) 
		FROM 
		film
	);
```

서브쿼리를 실행하는 순서는 다음과 같다.
> 첫째, 서브쿼리를 실행한다. 둘째 결과를 가져와 외부 쿼리에 전달한다. 마지막으로 외부 쿼리를 실행한다.

* IN을 사용한 서브쿼리

```
SELECT
   film_id,
   title
FROM
   film
WHERE
   film_id IN (
      SELECT
         inventory.film_id
      FROM
         rental
      INNER JOIN inventory ON inventory.inventory_id = rental.inventory_id
      WHERE
         return_date BETWEEN '2005-05-29'
      AND '2005-05-30'
   );
```

## EXISTS 연산자를 사용하는 서브쿼리

EXISTS 연산자는 서브쿼리에서 반환되는 값이 있는지(있으면 true) 없는지(없으면 false)만 판단한다. EXISTS는 서브쿼리에서 반환된 행의 수만 신경쓰며
행의 내용에는 신경을 쓰지 않는다.

아래와 같은 코드 형태로 주로 사용된다.
```
EXISTS (SELECT 1 FROM tb1 WHERE condition);
```

```
SELECT
   first_name,
   last_name
FROM
   customer
WHERE
   EXISTS (
      SELECT
         1
      FROM
         payment
      WHERE
         payment.customer_id = customer.customer_id
   );
```

## PostgreSQL ANY(SOME) Operator

ANY 연산자는 서브쿼리에서 반환된 값들과 비교를 한다. 
```
expression operator ANY(subquery)
```

> 서브쿼리는 정확히 하나의 열을 반환해야 한다.
> ANY 연산자 앞에는 반드시 비교 연산자(=, <=, >, < and <>)가 있어야 한다.
> ANY 연산자는 서브쿼리가 조건을 충족시켰을때 true, 아니면 false를 반환한다.

또한 SOME 연산자도 ANY 연산자와 동일한 역할을 하기에 서로 대체해서 사용해도 된다.

```
SELECT title
FROM film
WHERE length >= ANY(
    SELECT MAX( length )
    FROM film
    INNER JOIN film_category USING(film_id)
    GROUP BY  category_id );
```
길이가 영화 카테고리의 최대 길이 보다 크거나 같은 영화를 찾는 서브쿼리로 ANY가 사용되었다. 각 영화 카테고리에 대해(GROUP BY category_id)
서브쿼리는 최대 길이보다 크거나 같은 영화 길이를 결정한다.

> 서브 쿼리가 행을 반환하지 않는 경우 전체 쿼리는 빈 결과 집합을 반환합니다.

### ANY vs IN

= ANY 는 IN 연산자와 같다. 

```
SELECT
    title,
	category_id
FROM
	film
INNER JOIN film_category
		USING (film_id)
WHERE 
	category_id = ANY( # 여기서 "= ANY"는 IN 으로 바꿔도 똑같은 결과가 나온다.
		SELECT
			category_id
		FROM
			category
		WHERE
			NAME = 'Action'
				OR NAME = 'Drama'
	);
```
장르의 카테고리가 Action 과 Drama 인 영화와 id를 추출하는 쿼리문이다.

* <> AMY 연산자는 NOT IN과 다르다. 

```
X <> ANY (a,b,c) 
```
는 아래의 식과 같다.
```
x <> a OR x <> b OR x <> c
```

## PostgreSQL ALL Operator

PostgreSQL ALL 연산자는 서브쿼리에서 반환되는 값 목록들을 바탕으로 쿼리를 할 수 있다.

```
comparison_operator ALL (subquery)
```

> ALL 연산자 앞에는 비교 연산자가 있어야 한다.(=,!=,>,<,<=)
> ALL 연산자는 괄호로 둘러 싸여진 서브쿼리가 뒤따라야 한다.

서브쿼리가 행을 반환한다면, ALL 연산자는 비교 연산자를 기준으로 true, false 를 반환한다.
서브쿼리가 행을 반환하지 않는 경우, ALL 연산자는 항상 true로 평가한다.

* 예시

평균 길이 목록보다 길이가 긴 필름들을 찾는다면, ALL 연산자에서 ">" 를 사용해야 한다.
```
SELECT
    film_id,
    title,
    length
FROM
    film
WHERE
    length > ALL (
            SELECT
                ROUND(AVG (length),2)
            FROM
                film
            GROUP BY
                rating
    )
ORDER BY
    length;
```

## PostgreSQL EXISTS

EXISTS 연산자는 서브쿼리의 존재하는 열들을 테스트 하기 위한 연산자다.

```
EXISTS (subquery)
```

EXISTS는 서브쿼리의 값들을 그대로 받아서, 서브쿼리가 하나 이상의 행을 반환하면 true, 반환하지 않는다면 false이다.

EXISTS는 종종 상관 관계가 있는 서브쿼리와 함께 사용된다. 또한 EXISTS 의 결과는 서브쿼리에서 행의 반환여부 이므로 서브쿼리의 SELECT문에서 선택하는
열은 크게 중요하지 않다.

```
SELECT first_name,
       last_name
FROM customer c
WHERE [NOT] EXISTS
    (SELECT 1 # EXISTS에서 SELECT 는 중요하지 않기에 1 만 쓰고 끝.
     FROM payment p
     WHERE p.customer_id = c.customer_id
       AND amount >= 11 )
ORDER BY first_name,
         last_name;
```

고객중 적어도 11번 이상 빌린 고객들을 뽑아내는 subquery를 EXISTS 문을 통해서 true, false를 반환하여 고객들의 이름을 반환하는 쿼리문이다

* NOT EXISTS는 EXISTS 의 반대 결과를 반환한다.

### EXISTS and NULL

서브쿼리에서 NULL을 반환하면, EXISTS는 true를 반환한다.

```
SELECT first_name,last_name
FROM customer
WHERE EXISTS(SELECT NULL)
ORDER BY
	first_name, last_name;
```

<img src="https://www.postgresqltutorial.com/wp-content/uploads/2017/08/PostgreSQL-EXIST-with-NULL-example.png">

위와 같이 SELECT 문에 NULL이 있어도 EIXSTS절이 true 로 인식하여 반환된다.

