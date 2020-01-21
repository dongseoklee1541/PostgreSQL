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
INNER JOIN payment ON payment.customer_id = customer.customer_id # 세 개의 테이블을 결합할때는 INNER JOIN을 두번 사용한다.
INNER JOIN staff ON payment.staff_id = staff.staff_id;
```


----

출처 : https://theartofpostgresql.com/blog/2019-09-sql-joins/
