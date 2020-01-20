# Querying Data

## PostgreSQL SELECT

PostgreSQL을 사용할때 가장 많이 사용하는 것은 테이블에 쿼리문을 날리는 것인데, 그중에서 가장 복잡한 문은 SELECT다.

### PostgreSQL SELECT syntax

```
SELECT
   column_1,
   column_2,
   ...
FROM
   table_name;
```
> 먼저 검색할(쿼리를 날릴) column(열)을 선택한다. 여러 열에서 검색하고자 한다면 쉼표를 통해서 구별하자. 모든 열에서 검색하고 싶다면 "*" 을 사용

> 데이터를 검색할 table의 이름은 FROM을 통해서 불러온다.

테이블의 열 외에도 문자 그대로의 값과 식을 사용할 수 있다. 

> SQL은 대소문자를 구별하지 않지만, 대문자를 사용하면 코드를 읽기 쉽다. ex) SELECT,select 둘 다 같은 효과

### SELECT examples

<img src="https://www.postgresqltutorial.com/wp-content/uploads/2019/05/customer.png">

* one column SELECT
``` 
SELECT 
   first_name
FROM 
   customer;
```
세미콜론(;)은 SQL문의 일부가 아니며, SQL 문의 끝이라는 의미로 쓰이며, 여러개의 SQL문을 구분하는 의미로도 사용된다.

* multiple column examples
```
SELECT
   first_name,
   last_name,
   email
FROM
   customer;
```

* all columns example
```
SELECT
   *
FROM
   customer;
```
다만 asterisk(*)를 사용하는건 좋은 방법이 아니다.

> 테이블의 열이 많은 경우, 불필요한 열도 함께 검색된다. 불필요한 열을 함께 검색한다면 데이터베이스와 애플리케이션 계층간에 트래픽이 증가하고
애플리케이션의 성능과 확장성이 저하된다. 따라서 필요한 column만 선택하는 것이 좋다.

* SELECT with expression example
```
SELECT 
   first_name    ' '    last_name AS full_name,
   email
FROM 
   customer;
```
<img src="https://www.postgresqltutorial.com/wp-content/uploads/2019/05/postgresql-select-with-expression-example.png">

* SELECT only expression example
```
SELECT 5 * 3 AS result;
```
<img src="https://www.postgresqltutorial.com/wp-content/uploads/2019/05/postgresql-select-with-an-expression-only.png">

----

## PostgreSQL ORDER BY

ORDER BY를 통해서 반환되는 값들을 정렬 할 수 있다.

```
SELECT
   column_1,
   column_2
FROM
   table_name
ORDER BY
   column_1 [ASC   DESC],
   column_2 [ASC   DESC];
```
> ORDER BY는 쉼표를 사용해서 열 별로 구별 및 지정을 할 수 있고, 오름차순(ASC)과 내림차순(DESC)를 사용할 수 있다. 기본값은 ASC 이다.

* ORDER BY sort rows by one column
```
SELECT
   first_name,
   last_name
FROM
   customer
ORDER BY
   first_name (ASC);
```

* ORDER BY sort rows by multiple columns

오름차순으로 이름을 기준으로 고객을 정렬하고, 성을 기준으로 정렬된 결과 집합을 내림차순으로 정렬하는 경우

```
SELECT
   first_name,
   last_name
FROM
   customer
ORDER BY
   first_name ASC,
   last_name DESC;
```

* ORDER BY sort rows by expression

LENGTH() 를 사용하여, 문자열을 받아서 그 길이를 반환하는 칼럼을 만든다.

```
SELECT 
   first_name,
   LENGTH(first_name) len
FROM
   customer
ORDER BY 
   LENGTH(first_name) DESC;
```

## PostgreSQL SELECT DISTINCT

DISTINCT는 SELECT 문에서 중복 rows를 제거하기 위해 사용된다. DISTINCT는 각 그룹에 대한 row를 하나씩만 남겨 놓는다. 
DISTINCT 절은 테이블의 하나 이상의 columns 을 대상으로 할 수 있다.

```
SELECT
   DISTNCT column_1, column_2
FROM
   table_name;
```
column_1,2의 값은 중복을 확인하는데 사용 된다.

```
SELECT
   DISTINCT ON (column_1) column_alias,
   column_2
FROM
   table_name
ORDER BY
   column_1,
   column_2;
```

DISTINCT ON (expression)은 중복 값들의 첫번째 항을 보여준다. 하지만 순서는 예측할 수 없이 랜덤하게 뽑으므로 ORDER BY 를 함께 사용하는 것이 좋다.

* PostgreSQL SELECT DISTINCT ex)
실습에 사용될 테이블을 만든다.

```
CREATE TABLE t1 (
   id serial NOT NULL PRIMARY KEY,
   bcolor VARCHAR,
   fcolor VARCHAR);
   
INSERT INTO t1 (bcolor, fcolor)
VALUES
   ('red', 'red'),
   ('red', 'red'),
   ('red', NULL),
   (NULL, 'red'),
   ('red', 'green'),
   ('red', 'blue'),
   ('green', 'red'),
   ('green', 'blue'),
   ('green', 'green'),
   ('blue', 'red'),
   ('blue', 'green'),
   ('blue', 'blue');
```

* PostgreSQL DISTINCT ON ex)
```
SELECT
   DISTINCT ON
   (bcolor) bcolor,
   fcolor
FROM
   t1
ORDER BY
   bcolor,
   fcolor;
```

<img src="https://www.postgresqltutorial.com/wp-content/uploads/2019/05/PostgreSQL-SELECT-DISTINCT-ON-example.png">
DISTINCT ON에 해당하는 칼럼을 기준으로 첫번째로 나오는 ROW들만 반환한다.

----

https://www.postgresqltutorial.com/postgresql-select
