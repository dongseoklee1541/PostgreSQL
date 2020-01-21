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

    
----

출처 : https://theartofpostgresql.com/blog/2019-09-sql-joins/
