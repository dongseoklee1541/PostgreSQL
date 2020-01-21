# PostgreSQL Joins

<img src="https://theartofpostgresql.com/img/SQL-JOINS-Example-0.png">

> PostgreSQL에서 지원하는 Joins은 inner,left,right,full outer, cross, natural, self-join 이 있다. 

Join은 테이블 간에 일반 열 값에 따라 하나 이상의 테이블에서 열을 결합하는데 사용된다. 일반적으로 공통 열은 첫번째 테이블은 Primary Key(기본 키)
열 및 두번째 테이블의 Foreign Key(외래 키)열이다.


<img src="https://www.postgresqltutorial.com/wp-content/uploads/2018/12/PostgreSQL-Joins.png">

* Joins 문 예시
```
SELECT
    a.id id_a,
    a.fruit fruit_a,
    b.id id_b,
    b.fruit fruit_b
FROM
    basket_a a
INNER(LEFT, RIGHT, FULL OUTER) JOIN basket_b b ON a.fruit = b.fruit;
```

이런 방식 외에 WHERE 문으로 조건을 걸어 왼쪽/오른쪽 테이블만 갖고 있는 열만 추출하는 Join 방법도 존재한다.

```
SELECT
    a.id id_a,
    a.fruit fruit_a,
    b.id id_b,
    b.fruit fruit_b
FROM
    basket_a a
LEFT JOIN basket_b b ON a.fruit = b.fruit # 여기까지가 LEFT JOIN, a.fruit는 기본키 b.fruit는 외래키
WHERE b.id IS NULL; # 이런 조건을 넣는다면, 왼쪽 테이블만 갖고 있는 열만 추출하는 LEFT JOIN이 된다.
```

## PostgreSQL INNER JOIN

<img src="https://www.postgresqltutorial.com/wp-content/uploads/2013/05/A-and-B-tables.png">

```
SELECT
   A.pka,
   A.c1,
   B.pkb,
   B.c2
FROM
   A
INNER JOIN B ON A .pka = B.fka;
```
A 테이블과 B 테이블을 결합하기 위해선
> SELECT문에서 두 테이블에서 합치고 싶은 열들을 선택한다.
> FROM문에서 주 테이블을 지정한다. 위의 예에선 A테이블이다.
> 마지막으로 기본테이블에 조인되는 테이블을 지정해 합칠 기본키(A.pka)와 외래키(B.fka)를 ON 조건으로 정한다.

<img src="https://www.postgresqltutorial.com/wp-content/uploads/2013/05/PostgreSQL-INNER-JOIN-Venn-Diagram.png">

* PostgreSQL INNER JOIN 예제

<img src="https://www.postgresqltutorial.com/wp-content/uploads/2013/05/customer-payment-staff-tables.png">

* 각 staff들은 payment와 없거나 관련이 있다. 각 payment는 한명의 직원이 처리한다.
* 각 customer들은 payment와 없거나 관련이 있다. 각 payment는 한명의 고객에게만 해당된다.

```
SELECT
   customer.customer_id,
   customer.first_name customer_first_name,
   customer.last_name customer_last_name,
   customer.email,
   staff.first_name staff_first_name,
   staff.last_name staff_last_name,
   amount,
   payment_date
FROM
   customer
INNER JOIN payment ON payment.customer_id = customer.customer_id 
# 세 개의 테이블을 결합할때는 INNER JOIN을 두번 사용한다.
INNER JOIN staff ON payment.staff_id = staff.staff_id;
```

## PostgreSQL LEFT JOIN

A 테이블과 B 테이블이 있을때, B에는 없고 A에만 있는 열을 추출하고자 할때 LEFT JOIN을 사용하면 좋다. B에 없는 열들은 NULL 값으로 대체 된다.

```
SELECT
   A.pka,
   A.c1,
   B.pkb,
   B.c2
FROM
   A
LEFT JOIN B ON A .pka = B.fka;
```
> SELECT 문에서 선택할 행을 선택한다.
> FROM 문에서 모든 열을 얻고자 하는 LEFT table을 선택한다.
> LEFT JOIN 문에서 조인 하고자 하는 테이블을 선택하고 조인할 조건을 명시한다.

* PostgreSQL LEFT JOIN 예제
<img src="https://www.postgresqltutorial.com/wp-content/uploads/2013/05/film-and-inventory-tables.png">

```
SELECT
   film.film_id,
   film.title,
   inventory_id
FROM
   film
LEFT JOIN inventory ON inventory.film_id = film.film_id
WHERE
   inventory.film_id IS NULL; # NULL인 녀석들을 찾아냄, 없으면 공통인 열만을 뽑아냄
```
<img src="https://www.postgresqltutorial.com/wp-content/uploads/2013/05/PostgreSQL-LEFT-JOIN-WHERE-IS-NULL.png">

## PostgreSQL Self-Join

self-join은 테이블 자체에 조인이 되는 쿼리문이다. 동일한 테이블 안에 있는 행의 열을 비교하는데 사용된다.

self-join을 사용하기 위해선 같은 테이블을 서로 다른 임의 별칭으로 설정한 뒤 INNER/LEFT/RIGHT JOIN을 통해서 실행하면 된다.

* Self-Join 예시
<img src="https://www.postgresqltutorial.com/wp-content/uploads/2018/03/film_table.png">

```
SELECT
	f1.title,
	f2.title,
	f1.length
FROM
	film f1
INNER JOIN film f2 ON f1.film_id <> f2.film_id
AND f1.length = f2.length;
```
같은 테이블 내에서의 비교 join을 통해서 타이틀의 이름은 다르지만 lengh가 같은 것들을 찾아내었다.

## PostgreSQL FULL OUTER JOIN

FULL OUTER JOIN은 left join 과 right join의 결과물의 결합이다. 즉 두 테이블의 일치하는 행과 일치하지 않는 행 모두 결과에 나타난다.
```
SELECT * FROM A
FULL [OUTER] JOIN B on A.id = B.id; # OUTER는 옵션이다.
```

```
SELECT
   employee_name,
   department_name
FROM
   employees e
FULL OUTER JOIN departments d ON d.department_id = e.department_id; # 이 경우 서로 공통되지 않는 ROW도 반환된다.
(WHERE employee_name[또는 d.department_id] IS NULL;) # 피고용자가 없는 부서[또는 부서가 없는 피고용자]를 검색하는 방법
```

```
  employee_name  | department_name
-----------------+-----------------
 Bette Nicholson | Sales
 Christian Gable | Sales
 Joe Swank       | Marketing
 Fred Costner    | HR
 Sandra Kilmer   | IT
 Julia Mcqueen   | NULL # NULL 인 값도 같이 나타난다.
 NULL            | Production
(7 rows)
```

## PostgreSQL Cross Join By Example

CROSS JOIN 을 사용해서 두개 이상의 테이블에 행이 있는 데카르트 곱(곱집합,cartesian product)를 만들 수 있다. cross join은
left join과 inner join처럼 join하는 데 있어서 조건이 없는 특징이 있다.

```
SELECT *
FROM T1
CROSS JOIN T2;
```
```
SELECT *
FROM T1, T2;
```
만약 조건이 true 라면, INNER JOIN 절을 통해서도 CROSS JOIN을 할 수 있다.
```
SELECT *
FROM T1
INNER JOIN T2 ON TRUE;
```

* 예제

```
SELECT
   *
FROM
   T1
CROSS JOIN T2;
```

<src img="https://www.postgresqltutorial.com/wp-content/uploads/2016/06/PostgreSQL-CROSS-JOIN-illustration.png">

## PostgreSQL NATURAL JOIN Explained By Examples

naural join은 공통 열(common column) 이름을 기준으로 암시적 조인을 만드는 것이다.
```
SELECT *
FROM T1
NATRUAL [INNER, LEFT, RIGHT] JOIN T2;
```
natural join은 inner/left/right join 모두 될 수 있고, 따로 명시하지 않는 한 기본값은 INNER JOIN이다.

> 만약 위의 예시처럼 SELECT * 를 사용한다면 양 테이블에 같은 이름인 열과 아닌 열들 모두 불러온다.

* 예제

```
CREATE TABLE categories (
   category_id serial PRIMARY KEY,
   category_name VARCHAR (255) NOT NULL
);
 
CREATE TABLE products (
   product_id serial PRIMARY KEY,
   product_name VARCHAR (255) NOT NULL,
   category_id INT NOT NULL,
   FOREIGN KEY (category_id) REFERENCES categories (category_id)
);
```

products 테이블의 category_id는 외래키로 categories 테이블의 기본키를 참조하고 있다. 그래서 category_id는 natrual join의 
공통 열(common column)이다.

```
SELECT
   *
FROM
   products
NATURAL JOIN categories; # INNER JOIN categories USING (category_id); 와 동일한 결과가 나온다.
```

NATURAL JOIN은 INNER JOIN과 다르게 조건(USING category_id)을 따로 사용하지 않아도 되는데, 이는 공통 열을 기준으로 join하기 때문이다.




----

출처 : https://theartofpostgresql.com/blog/2019-09-sql-joins/
