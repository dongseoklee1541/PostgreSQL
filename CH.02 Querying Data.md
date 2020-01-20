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



----

https://www.postgresqltutorial.com/postgresql-select/
