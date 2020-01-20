# PostgreSQL WHERE

쿼리문에서 조건을 넣고 싶다면 "WHERE"을 사용하면 된다.

```
SELECT select_list
FROM table_name
WHERE condition;
```
WHERE 문은 FROM 문 다음에 사용해야 한다. WHERE의 조건문은 true,false 또는 unknown이 되어야 한다. 쿼리문은 WHERE 절의 조건에 true 인 행만 
반환한다.

<img src="https://github.com/dongseoklee1541/PostgreSQL/blob/master/image/143710.png?raw=true" width="480" hegiht="640">

* WHERE with IN Examples
테이블에 있는 문자열과 쿼리문의 문자열을 일치 시키려면 IN 을 사용하면 된다.
```
SELECT
   first_name,
   last_name
FROM
   customer
WHERE 
   first_name IN ('Ann','Anne','Annie');
```
<img src="https://www.postgresqltutorial.com/wp-content/uploads/2019/05/PostgreSQL-WHERE-with-IN-operator.png">4

* WHERE with LIKE Examples
조건에 걸리는 패턴과 일치하는 문자열을 찾으려면 LIKE를 사용하면 된다.
```
SELECT
   first_name,
   last_name
FROM
   customer
WHERE 
   first_name LIKE 'Ann%'
```
여기서 "Ann%"은 Ann 뒤에 무엇이 나오더라도 모두 받겠다는 의미로 사용 한다.

* WHERE with BETWEEN
범위 값을 주고 싶다면 BETWEEN 연산자를 사용 한다.
```
SELECT
   first_name,
   LENGTH(first_name) name_length
FROM
   customer
WHERE 
   first_name LIKE 'A%' AND
   LENGTH(first_name) BETWEEN 3 AND 5
ORDER BY
   name_length;
```

* WHERE with <>

<> condition 은 조건과 같지 않는 녀석들을 반환하는 기능이다.

```
SELECT 
   first_name, 
   last_name
FROM 
   customer 
WHERE 
   first_name LIKE 'Bra%' AND 
   last_name <> 'Motley';
```
이 조건에서는 first_name이 'Bra'로 시작하고 last_name이 'Motley'가 아닌 녀석들을 찾는다.

# PostgreSQL LIMIT

LIMIT는 SELECT 문의 subset of rows를 반환하는 조건문이다. 만약 특정 갯수의 행을 뛰어넘고 싶다면 OFFSET 을 사용하면 된다.
```
SELECT
   *
FROM
   table
LIMIT n OFFSET m;
```
n개를 리턴하기전에, m개의 rows를 건너뛰고 실행 된다.  m이 0이라면 OFFSET문은 없는 것처럼 처리하고 실행 된다. 다만 DB TABLES은 행의 순서가 없기에,
LIMIT 절을 사용 할 때 ORDERBY 절을 사용해야 한다.

* 4번째 영화부터 3개를 추출하는 LIMIT 문

```
SELECT
	film_id,
	title,
	release_year
FROM
	film
ORDER BY
	film_id
LIMIT 4 OFFSET 3;
```

# PostgreSQL FETCH

LIMIT 문은 유용하지만 SQL 표준이 아니다. PostgreSQL은 SQL 표준을 준수하기 위해 "FETCH"를 사용 한다. FETCH는 쿼리문에 의해 반환된 행의 일부를
검색하기 위한 것이다.

```
OFFSET start { ROW   ROWS }
FETCH { FIRST   NEXT } [ row_count ] { ROW   ROWS } ONLY
```

> ROW, FIRST & ROWS / NEXT 는 각각 똑같은 의미니 취향것 쓰자.
> start는 0 또는 양수가 들어와야 한다.  기본적으로 OFFSET이 지정되어 있지 않다면 start는 0 이다. 만약 start의 값이 쿼리문의 반환값의 개수 보다
크다면 아무 행도 반환되지 않는다.
> row_count 는 1보다 크거나 같다. 기본값은 1이다.

* PostgreSQL FETCH ex)

```
SELECT
    film_id,
    title
FROM
    film
ORDER BY
    title 
OFFSET 5 ROWS 
FETCH FIRST 5 ROW ONLY; 
```
<img src="https://www.postgresqltutorial.com/wp-content/uploads/2018/03/PostgreSQL-FETCH-NEXT-5-ROWS-ONLY-OFFSET-5-ROWS.png">
5개의 열을 skip 하고, 5개를 추출하는 쿼리문

# PostgreSQL IN

```
value IN (value1, value2,...)
value IN (SELECT value FROM tb1_name); # 다른 쿼리안에 중첩해서 사용하는 쿼리문을 Subquery(하위 쿼리)라고 한다.
```
IN은 WHERE절에서 들어온 값들이 True인지 아닌지 판별하는 역할을 한다. 값의 목록의 값(value1, value2)과 일치하면 true를 반환하는 식이다.
list of values는 문자열 또는 숫자 모두 가능하다.

* 예제

고객 ID가 1과 2인 고객의 대여 정보를 알고 싶다면 WHERE 절을 주목하자.
```
SELECT
	customer_id,
	rental_id,
	return_date
FROM
	rental
WHERE
	customer_id IN (1,2) # 또는 customer_id = 1 or customer_id = 2 사용
ORDER BY
	return_date DESC;
```

### PostgreSQL NOT IN

IN과 NOT을 조합 할 수 있다. NOT IN은 조건에 맞지 않는 rows을 반환한다.

```
SELECT
	customer_id,
	rental_id,
	return_date
FROM
	rental
WHERE
	customer_id NOT IN (1,2); # customer_id <> 1 AND customer_id <> 2 와 동일하다.
```

고객 ID목록을 서브쿼리로 추출하여 조건으로 사용 할 수 있다.
```
SELECT
   first_name,
   last_name
FROM
   customer
WHERE
   customer_id IN (
      SELECT
         customer_id
      FROM
         rental
      WHERE
         CAST (return_date AS DATE) = '2005-05-27'
   );
```

## PostgreSQL BETWEEN

BETWEEN은 사이 값을 추출할때 사용하는 WHERE문의 조건문이다. WHERE 절에 있는 SELECT, INSERT, UPDATE or DELETE 와 자주 사용된다.
```
value BETWEEN low AND high;
value >= low and value <= high;
value NOT BETWWEN low AND high;
value < low OR value > high;
```

* 예제

```
SELECT
   customer_id,
   payment_id,
   amount,
 payment_date
FROM
   payment
WHERE
   payment_date BETWEEN '2007-02-07'
AND '2007-02-15';
```
날짜의 범위에 대한 값을 사용하려면 YYYY-MM-DD 의 리터럴 날짜를 사용해야 한다.

## PostgreSQL (NOT) LIKE

LIKE문은 WHERE 조건문에 사용되는 절이며, 검색한 값과 비슷한 결과를 얻고자 할때 사용 되며, 특별한 와일드카드가 제공된다.
> % 를 사용하여 문자의 순서를 정할 수 있다.
> _ 를 사용하여 단일 문자(single character)와 매칭시킨다.
> 와일드 카드를 사용하지 않으면,  = 연산자와 동일한 역할을 한다.

* 예시

```
SELECT
	first_name,
	last_name
FROM
	customer
WHERE
	first_name LIKE '_her%';
```
her 앞에는 한글자만 들어오고, 뒤의 글자수는 제한이 없는 조건이다.

### PostgreSQL ILIKE
LIKE는 대소문자 구별을 하지만, ILIKE 연산자는 대소문자를 구별하지 않고, 값을 일치 시킨다.


## PostgreSQL IS (NOT) NULL

IS NULL은 값이 NULL 인지 아닌지 체크하는 연산자이다. 

```
WHERE phone = NULL; # 아무것도 반환하지 않는다. NULL과 동일하다는 연산자는 항상 false를 반환하기 때문이다.
WHERE pohne IS NULL; # NULL 인지 아닌지 체크하는 연산자 이므로 NULL 인 값들만 반환
``` 

## PostgreSQL Alias

쿼리문안에서 특정 column을 'AS'를 사용하여 임시로 이름을 할당 할 수 있다. 이렇게 할당된 임시 이름은 쿼리문이 실행 중에만 존재한다.

```
SELECT column_name AS alias_name
FROM table;
```
