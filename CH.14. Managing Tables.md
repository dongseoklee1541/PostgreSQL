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

위와 같은 쿼리문은 아래와 같다.

```
CREATE SEQUENCE table_name_id_seq;

CREATE TABLE table_name(
	id integer NOT NULL DEFAULT nextval('table_name_id_seq')
);

ALTER SEQUENCE table_name_id_seq
OWNED BY table_name.id;
```

PostgreSQL은 세가지의 시리얼 타입이 존재한다.

* SAMLLSERIAL : 2 bytes, 1 to 32,767
* SERIAL : 4 bytes, 1 to 2,147,483,647
* BIGSERIAL : 8 bytes, 1 to 9,223,372,036,854,775,807

 
 * Serial 예시
 
fruits 테이블에 id를 만드는 예시다.
 
```
CREATE TABLE fruits(
   id SERIAL PRIMARY KEY,
   name VARCHAR NOT NULL
);
```

```
INSERT INTO fruits(name) 
VALUES('Orange');

OR

INSERT INTO fruits(id,name) 
VALUES(DEFAULT,'Apple');
```

만약 시리얼(serial) 숫자를 알고 싶다면,
```
pg_get_serial_sequence('table_name','column_name')
# 예시 SELECT currval(pg_get_serial_sequence('fruits', 'id'));
```
를 사용하면 된다. INSERT 하면서 몇번째로 들어가는지 알고 싶다면, **RETURNING** 을 사용하자.

```
INSERT INTO fruits(name) 
VALUES('Banana')
RETURNING id;
```

## PostgreSQL Sequences

Sequences 란 숫자의 나열을 의미한다. {1,2,3,4,5} 또는 {5,4,3,2,1} 과 같이

```
CREATE SEQUENCE [ IF NOT EXISTS ] sequence_name
	[ AS {SMALLINT | INT | BIGINT } ]
	[ INCREMENT [ BY ] increment]
	[ MINVALUE minvalue | NO MINVALUE ]
	[ MAXVALUE maxvalue | NO MAXVALUE ]
	[ START [ WITH ] start ]
	[ CACHE cache ]
	[ [ NO ] CYCLE ]
	[ OWNER BY {table_name.colum_name | None } ]
```

> sequence_name : sequence의 이름을 CREATE SEQUENCE 뒤에 명시한다. IF NOT EXISTS 옵션은 중복의 경우 나타나는 에러를 막기 위해서 사용한다.
sequence_name은 다른 sequences, tables, indexes, view, 또는 같은 스키마에 있는 외부(foreign)테이블과도 구별되어야 한다.

> AS { SMALLINT | INT | BIGINT } : 기본 값은 BIGINT로 생략해도 된다. sequence의 데이터 타입을 정해준다. 

> CYCLE | NO CYCLE : 최대값을 넘어가 버리면, 순환하여 처음 값으로 돌아오느냐 아니냐의 설정, 기본값은 NO CYCLE

* 예시

#### 오름 차순이며, 5씩 증가하는 sequence 문
```
CREATE SEQUENCE mysequence
INCREMENT 5
START 100;
```

nextval()를 통해서 값을 얻어보자.
```
SELECT nextval('mysequence'); # 이때의 값은 100

SELECT nextval('mysequence'); # 이때의 값은 105 이다.
```

#### 내림차순
```
CREATE SEQUENCE three
INCREMENT -1 # 숫자가 내려가고
MINVALUE 1
MAXVALUE 3
START 3 # 최대 값 부터 시작한다.
CYCLE;
```

#### 테이블에 sequence를 연결해 보자.

```
CREATE TABLE order_details(
    order_id SERIAL,
    item_id INT NOT NULL,
    product_id INT NOT NULL,
    price DEC(10,2) NOT NULL,
    PRIMARY KEY(order_id, item_id)
);

# 그리고 sequence를 정의하자.

CREATE SEQUENCE order_item_id
START 10
INCREMENT 10
MINVALUE 10
OWNED BY order_details.item_id;

# 정의한 sequence를 INSERT 하여 테이블에 집어넣는다.

INSERT INTO 
    order_details(order_id, item_id, item_text, price)
VALUES
    (100, nextval('order_item_id'),'DVD Player',100),
    (100, nextval('order_item_id'),'Android TV',550),
    (100, nextval('order_item_id'),'Speaker',250);
```

그 결과, Sequence 에서 정의한 대로 item_id의 값이 정해진다.

#### 시퀸스 삭제

sequence가 테이블 열과 연결된 경우 테이블 열 또는 전체 테이블이 삭제 되면 자동으로 삭제 된다. 그렇지 않다면, 직접 삭제해야 한다.
```
DROP SEQUENCE [ IF EXISTS ] sequence_name [, ...]
[ CASCADE RESTRICT ];
```

> 먼저 삭제할 시퀸스의 이름을 정한다. IF EXISTS 조건으로 존재하는 시퀸스를 삭제 할 수 있다. 여러 시퀸스를 한번에 삭제 할때는, 컴마를 통해서 삭제를
하면 된다.

> CASCADE : 순서에 따라 달라지는 개체와 이러한 개체에 종속된 개체 등을 자동으로 삭제하려는 경우

## PostgreSQL Identity Column

PostgreSQL에서 제공하는 Serial 은 SQL 표준이 아니다. SQL 표준을 지키기 위해선, SQL 표준으로 제시된 **GENRATED AS IDENTITY** 를 사용해야 한다.

```
column_name type GENERATED { ALWAYS | BY DEFAULT } AS IDENTITY [ ( sequence_option ) ]
```

* 데이터 타입은 SMALLINT, INT or BIGINT

* GENERATED ALWAYS 는 identity column을 생성하는 명령어다. GENERATED ALWAYS AS IDENTITY 에 만약 INSERT 나 UPDATE를 할 경우 에러가 발생
한다.

* GENERATED BY DEFAULT 는 ALWAYS 와 기능이 같지만, insert 나 update를 하면, 에러가 나지 않고 시스템 생성 값 대신 입력한 값으로 대체 된다.

PostgreSQL 을 사용하면, 테이블에 두개이상의 id 열을 가질 수 있다. SERIAL 과 마찬가지로 GENERATED AS IDENTITY 는 내부에선 SEQUENCE를 사용한다.


* GENERATED ALWAYS 예시

```
CREATE TABLE color (
	color_id INT GENERATED ALWAYS AS IDENTITY,
	color_name VARCHAR NOT NULL
);

INSERT INTO color (color_name)
VALUES
	('Red');
```

```
INSERT INTO color (color_id, color_name)
VALUES
    (2, 'Green');  ==> 에러 발생, 시스템에서 거부한다.
    
# 해결 방법

INSERT INTO color (color_id, color_name)
OVERRIDING SYSTEM VALUE 
VALUES
    (2, 'Green');

# 또는 GENREATED BY DEFAULY AS IDENTITY를 사용하자.
```

* GENERATED BY DEFAULT AS IDENTITY 예시

기존 color 테이블을 drop 하고 다시 만들자.

```
DROP TABLE color;

CREATE TABLE color(
	color_id INT GENERATED BY DEFAULT AS IDENTITY,
	color_name VARCHAR NOT NULL
);

INSERT INTO color (color_id, color_name)
VALUES
    (2, 'Yellow'); => 정상적으로 작동 한다.
```

* SEQUENCE options 예시

https://www.postgresqltutorial.com/postgresql-identity-column/ 참조.


## PostgreSQL ALTER TABLE

존재하는 테이블의 구조를 바꾸고 싶다면 **ALTER TABLE** 을 사용하면 된다. 

```
ALTER TABLE table_name action;
```

* action으로 들어올 수 있는 것들 : Add / drop / rename a 열(column) 또는 열의 데이터 타입 변경이다. 또한 열의 기본값 수정 check 제약 조건 추가,
테이블 이름 바꾸기가 있다.

```
ALTER TABLE table_name ADD COLUMN new_column_name TYPE;

ALTER TABLE table_name DROP COLUMN column_name;

ALTER TABLE table_name RENAME COLUMN column_name TO new_column_name;

ALTER TABLE table_name ALTER COLUMN column_name [SET DEFAULT value | DROP DEFAULT];

ALTER TABLE table_name ALTER COLUMN column_name [SET NOT NULL| DROP NOT NULL];

ALTER TABLE table_name ADD CHECK expression;

ALTER TABLE table_name ADD CONSTRAINT constraint_name constraint_definition;

ALTER TABLE table_name RENAME TO new_table_name;
```

* PostgreSQL ALTER TABLE 예시

https://www.postgresqltutorial.com/postgresql-alter-table/ 참조


## PostgreSQL Rename Table : A Step-by-Step Guide

```
ALTER TABLE [ IF EXISTS ] table_name
RENAME TO new_table_name;
```

여러 테이블을 한번에 바꿀때에는, ALTER TABLE RENAME TO 문장을 반복해야 한다.


## PostgreSQL ADD COLUMN : Add One Or More Column To a Table

테이블에 열(Column)을 추가하고 싶다면, **ALTER TABLE ADD COLUMN** 을 사용하자.

```
ALTER TABLE table_name
ADD COLUMN new_column_name data_type;
```

열을 한번에 여러열을 추가하고 싶다면, ADD COLUMN을 반복하면 된다. 

```
ALTER TABLE custoemr 
 ADD COLUMN fax VARCHAR,
 ADD COLUMN email VARCHAR; # 이런식으로 여러 열을 한번에 추가
```

만약 열을 추가할때 NOT NULL 제약을 넣으면, 오류가 생긴다. PostgreSQL은 열이 추가 될때 기본값으로 NULL을 받기 때문이다.

이를 해결하기 위해선 첫번째, 값을 다 넣어 주고 난 뒤에 제약조건을 거는 방법이 있다.

```
ALTER TABLE customers 
ADD COLUMN contact_name VARCHAR NOT NULL;

UPDATE customers
SET contact_name = 'John Doe'
WHERE
   ID = 1;
 
UPDATE customers
SET contact_name = 'Mary Doe'
WHERE
   ID = 2;
 
UPDATE customers
SET contact_name = 'Lily Bush'
WHERE
   ID = 3;
   
ALTER TABLE customers
ALTER COLUMN contact_name SET NOT NULL; # value를 다 채워넣은 뒤 NOT NULL 설정.
```

두번째 방법은 기본값을 설정해서 생성한 뒤, 나중에 기본값을 제거하는 방식이다.

```
ALTER TABLE customers
ADD COLUMN contact_name NOT NULL DEFAULT 'foo';

# contact 열 기본값 제거
ALTER TABLE customers
ALTER COLUMN contact_name
DROP DEFAULT;
```

## PostgreSQL DROP COLUMN: Remove One or More Columns of a Table

```
ALTER TABLE table_name 
DROP COLUMN [ IF EXISTS ] column_name [ CASCADE ];
```

열 제거는 다른 객체 (views, triggers, stored procedures..) 의해서 사용되는 경우, 열 제거는 안된다. 다른 객체들에게 종속되어
있기 때문이다. 이때 참조하는 객체까지 함께 지우는 CASCADE를 사용하면 열 제거가 가능하다.

IF EXISTS 문을 사용해서 열이 없을때 생기는 오류를 방지할 수 있다.

## PostgreSQL Change Column Type: Step-by-Step Examples

열의 데이터 타입을 변경하기 위해선 ALTER TABLE 을 수행해야 한다.
```
ALTER TABLE table_name
ALTER COLUMN column_name [SET DATA] TYPE new_data_type [ USING expression ];
```

> 먼저 변경할 열이 속한 테이블의 이름을 지정하고, 데이터 유형이 변경될 열 이름을 지정한다. 마지막으로 TYPE 키워드를 통해 새 데이터 유형을
제공한다.

> USING 문을 사용하면, 데이터 타입을 변경하면서 열의 값을 새 값으로 바꿀 수 있다.


* PostgreSQL change column type 예제

<img src="https://sp.postgresqltutorial.com/wp-content/uploads/2017/02/PostgreSQL-Change-Column-Type-Sample-Table.png">

```
CREATE TABLE assets (
    id serial PRIMARY KEY,
    name TEXT NOT NULL,
    asset_no VARCHAR NOT NULL,
    description TEXT,
    LOCATION TEXT,
    acquired_date DATE NOT NULL
);
 
INSERT INTO assets (
    NAME,
    asset_no,
    location,
    acquired_date
)
VALUES
    (
        'Server',
        '10001',
        'Server room',
        '2017-01-01'
    ),
    (
        'UPS',
        '10002',
        'Server room',
        '2')

```

여기서 name의 데이터 타입을 VARCHAR 로 바꿀려면,
```
ALTER TABLE asset ALTER COLUMN name TYPE VARCHAR;
```

## PostgreSQL RENAME COLUMN: Renaming One or More Columns of a Table

열의 이름을 바꾸기 위해선 ALTER TABLE RENAME TO 를 사용하면 된다.
```
ALTER TABLE table_name
RENAME [ COLUMN ] column_name TO new_column_name;
```

COLUMN 은 생략할 수 있다. 또한 열의 이름을 바꿀때에는 IF EXISTS를 지원하지 않는다. 없는 열을 바꿀때는 오류가 생긴다.

여러개의 열 이름을 동시에 바꿀때는 ALTER TABLE RENAME TO 를 반복해야 한다.
```
ALTER TABLE table_name
RENAME column_name_1 TO new_column_name_1;
 
ALTER TABLE table_name
RENAME column_name_2 TO new_column_name_2;
```

## PostgreSQL DROP TABLE

```
DROP TABLE [IF EXISTS] table_name [CASCADE | RESTRICT];
```

> RESTRICT는 테이블이 참조되고 있다면, 삭제를 거부하는 옵션이다. 기본값으로 설정되어 있다. 여러개의 테이블을 동시에 삭제하고 싶다면, 쉼표를 통해서제거하면 된다. 이 기능은 superuser, schema owner 그리고 table owner 만 실행할 수 있다.

## PostgreSQL TRUNCATE TABLE

테이블의 데이터를 제거하기 위해선, DELETE 를 사용하면 된다. 하지만 큰 테이블에서는 **TRUNCATE TABLE** 이 더 효과적이다.

TRUNCATE TABLE은 테이블의 모든 행을 읽지도 않고 모두 제거한다. 이것이 DELETE 보다 더 빠른 이유다.

```
TRUNCATE TABLE table_name [ RESTART IDENTITY ];
```
만약 테이블의 시퀸스까지 리셋하고 싶다면, RESTART IDENTITY을 사용하면 된다.

#### 외래키를 갖고 있는 테이블로부터 모든 데이터 제거하기

기본적으로 **TRUNCATE TABLE** 에서는 외래 키를 참조하는 데이터를 지우지 않는다. 이를 위해선 **CASCADE** 옵션이 필요하다.

```
TRUNCATE TABLE table_name CASCADE;
```

CASCADE 옵션은 의도치 않은 데이터까지 삭제할 수 있으니 주의해야 한다.

## A Step-by-Step Guide To PostgreSQL Temporary Table

Temporary Table 은 데이터베이스의 세션 기간동안에만 존재하고 세션이 끝날때 같이 삭제되는 테이블이다.

```
CREATE [ TEMPORARY | TEMP ] TABLE temp_table(
    ...
);
```

temporary table은 만든 세션에서만 볼 수 있다. 다른 세션에서는 보이지 않는다. 또한 temp table은 일반 테이블과 같은 이름을 사용 할 수 있다.(추천 하지는 않는다) 이 경우 temp table이 삭제 될 때까지 일반 테이블에 접근할 수 없다.

### TEMP TABLE 제거

temp table을 제거하는 것은 만드는것과 다르게 그냥 **DROP TABLE** 을 사용하면 된다.
```
DROP TABLE temp_table_name;
```

## PostgreSQL Copy Table: A Step-by-Step Guide with Practical Examples

table을 복사하기 위해서, 
```
CREATE TABLE new_table AS
TABLE existing_table;
[WITH NO DATA] # 데이터는 복사하지 않을때
```

테이블의 일부분만 복사하고 싶다면?
```
CREATE TABLE new_table AS 
SELECT
*
FROM
    existing_table
WHERE
    condition;
```

다만, 기존 테이블의 구조와 데이터는 복사해 오지만, 인덱스와 조건(Primary Key, UNIQUE 와 같은 것들)을 복사해오지 않는다.

*
```
CREATE TABLE contacts(
    id SERIAL PRIMARY KEY,
    first_name VARCHAR NOT NULL,
    last_name VARCHAR NOT NULL,
    email VARCHAR NOT NULL UNIQUE
);

INSERT INTO contacts(first_name, last_name, email) 
VALUES('John','Doe','john.doe@postgresqltutorial.com'),
      ('David','William','david.william@postgresqltutorial.com');
```

위의 테이블을 복사하자.

```
CREATE TABLE contact_backup 
AS TABLE contacts;
```
contact_backup 은 테이블의 구조와 값들은 잘 복사해 왔지만, PRIMARY KEY, UNIQUE 와 같은 조건들은 같이 가져오지 않는다. 후에 따로 추가 해줘야 한다.

```
ALTER TABLE contact_backup ADD PRIMARY KEY(id);
ALTER TABLE contact_backup ADD UNIQUE(email);
```

