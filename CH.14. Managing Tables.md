# PostgreSQL Data Types

PostgreSQL은 아래와 같은 데이터 타입을 지원한다.

* Boolean
* Character types such as char,varchar, and text.
* Numeric types such as **integer** and **floating-point number**
* Temporal types such as data, time, timestamp, and interval
* UUID for storing Universally Unique Identifiers
* Array for storing array strings, numbers, etc.
* JSON stores JSON data
* hstore stores key-value pair
* Special types such as network address and geometric data.

# CREATE TABLE

테이블을 만들기 위해선, **CREATE TABLE** 을 사용해야한다.

```
CREATE TABLE table_name(
  column_name TYPE column_constraint,
  table_constraint table_constraint
) INHERITS existing_table_name;
```

> CREATE TABLE 뒤에 새로 만들 테이블의이름을 적는다. 그리고 열의 이름, 데이터 타입, 그리고 필요한 경우 'NOT NULL'과 같은 강제 조항을 적어주자.
열 리스트를 다 작성한 후에, 테이블의 접근권한을 정해줘야 한다. 마지막으로 새로운 테이블이 이미 존재하는 테이블을 상속 받고 있다면, INHERITS 뒤에
상속할 테이블을 적어주자.

### PostgreSQL column constraints

* NOT NULL : 열의 값은 NULL 이 될 수 없다.
* UNIQUE : 열의 값은 전체 테이블에 대해 유일해야 한다. 하지만 PostgreSQL에서는 NULL을 각자 UNIQUE 한 것으로 취급하므로 여러개의 NULL이 있을 수
도 있다. 
* PRIMARY KEY : NOT NULL + UNIQUE , 다만 위와 마찬가지로 NULL은 많을 수도 있다.
* CHECK : INSERT or UPDATE 시 조건을 줄 수 있다. 예를들어 product 테이블의 price 열은 무조건 양수여야 한다.
* REFERENCES : 다른 테이블의 열에 존재하는 열의 값을 제한한다. 외래 키 제약 조건을 정의하기 위해서 사용하면 된다.

### PostgreSQL table constraints

테이블 제약 조건은 개별 열이 아닌 테이블 전체에 제약조건을 걸리는 것만 다르고 열 제약조건과 비슷하다.

* UNIQUE (column_list) : 괄호 안에 나열된 열들에게 고유 값을 가지도록 한다
* PRIMAR KEY(column_list) : 여러 열들에게 고유 키를 갖게 한다.
* CHECK(condition) : INSERT or UPDATE 시 조건을 확인한다.
* REFERENCES : 다른 테이블에 존재하는 열의 값을 제약한다.


* CREATE TABLE 예시
```
<img src="https://sp.postgresqltutorial.com/wp-content/uploads/2013/05/postgresql-create-tables-many-to-many.jpg">

role, account 는 만들어져 있다고 가정한다.

CREATE TABLE account_role
(
	user_id integer NOT NULL,
	role_id integer NOT NULL,
	grant_date timestamp without time zone,
	PRIMARY KEY (user_id, role_id), # Table-level constraint
	CONSTRAINT account_role_role_id_fkey FOREIGN KEY (role_id)
		REFERENCES role (role_id) MATCH SIMPLE
		ON UPD4ATE NO ACTION ON DELETE NO ACTION,
	CONSTRAINT account_role_user_id_fkey FOREIGN KEY (user_id)
		REFERENCES account (user_id) MATCH SIMPLE
		ON UPDATE NO ACTION ON DELETE NO ACTION
	);
```

## PostgreSQL SELECT INTO

SELECT INTO 를 사용하면, SELECT 쿼리문으로 반환된 데이터를 새로 만든 테이블에 넣을 수 있다. 새 테이블의 열의 이름과 데이터 타입은 SELECT문의
반환된 내용에 따른다. SELECT 문과 다르게 SELECT INTO는 클라이언트에게 데이터를 주지 않는다.

```
SELECT
	column_list
INTO [ TEMPORARY | TEMP | UNLOGGED ] [ TABLE ] new_table_name
FROM
	table_name
WHERE
	condition;
```

> SELECT 문의 결과로 새로운 테이블을 만들고 싶다면 **new_table_name** 에 해당 테이블 명을 넣자. 

> TEMP or TEMPORARY 키워드는 옵션이며,  temporary table을 만드는 것을 허용한다. 

> UNLOGGED는 unlogged table을 만들고 싶을때 사용한다.

> WHERE 절은 데이터를 받아올 기존 테이블의 데이터를 제한할 수 있으며 INNER JOIN, LEFT JOIN, GROUP BY, HAVING 과 같은 것들도 사용할 수 있다.


## PostgreSQL CREATE TABLE AS

CREATE TABLE AS 는 쿼리에 의해 반환된 데이터를 바탕으로 새로운 테이블을 만드는 것이다.

```
CREATE [TEMP] TABLE [IF NOT EXISTS] new_table_name ( column_name_list )
AS query;
```

> TEMP의 경우 temporary table을 의미한다. column_name_list는 기존 테이블의 column name과 다른 이름을 쓰고 싶다면 사용할 수 있다.
IF NOT EXISTS는 새로 만드는 테이블이 이미 있는 이름의 테이블일 경우 에러를 피하기 위한 방법이다.

* 예시

SELECT 문에 식이 있다면(여기에선 count), 열의 이름을 새로 지정해주는 것이 좋다.
```
CREATE TABLE IF NOT EXISTS film_rating (rating, film_count) 
AS 
SELECT
    rating,
    COUNT (film_id) # film_count 로 덮어 씌우기.
FROM
    film
GROUP BY
    rating;
```

## Using PostgreSQL SERIAL To Create Auto-increment Column

PostgreSQL에서 sequence(SERIAL)는 특별한 종류의 데이터베이스 객체이다. 이것은 정수의 순서를 보장하고 종종 기본키로 사용된다.

```
CREATE TABLE table_name(
  id SERIAL
);
```

