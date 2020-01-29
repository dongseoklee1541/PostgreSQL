# Insert

새로운 테이블을 만들기만 하면, 아무 데이터도 들어있지 않다. 대게 첫번째로 하는 것은 새로운 행을 테이블에 넣는 일이다. PostgreSQL은 INSERT를 통해서
이런 행위를 도와준다.

```
INSERT INTO table (column1, column2, …)
VALUES
   (value1, value2, …),
   (value1, value2, …) ,...;
```

이것이 기본형이고, 다른 테이블에서 행을 가져 오겠다면 

```
INSERT INTO table(column1, clumn2, ...)
SELECT column1, column2, ...
FROM another_table
WHERE condition;
```

### INSERT 예시

O'Reilly Media 처럼, 문자열에 single quote를 넣고 싶다면 아래와 같은 방식으로 하면 된다.
```
INSERT INTO link (url, name)
VALUES
   ('http://www.oreilly.com','O''Reilly Media');
```

<img src="https://sp.postgresqltutorial.com/wp-content/uploads/2013/06/postgresql-insert-string-with-single-quote.png">

### INSERT NEW Column Example

last_update 라는 이름의 새로운 열을 link 테이블에 추가 해보자. last_update 의 기본값은 CURRENT_DATE 함수를 사용한다.

```
ALTER TABLE link ADD COLUMN last_update DATE;

ALTER TABLE link ALTER COLUMN last_update SET DEFAULT CURRENT_DATE;
```

new row('last_update')는 YYYY-MM-DD 형식으로 저장되야 한다.
```
INSERT INTO link (url, name, last_update)
VALUES
  ('http://www.facebook.com','Facebook','2013-06-01');
```

<img src="https://sp.postgresqltutorial.com/wp-content/uploads/2013/06/postgresql-insert-date-example.png">

물론 DEAFULT 키워드를 사용해서 오늘 날짜로 지정하는 방법도 있다.

```
INSERT INTO link (url, name, last_update)
VALUES
  ('https://www.tumblr.com/','Tumblr',DEFAULT);
```

### insert data from another table 예시

link 와 구조가 똑같은 link_tmp 라는 테이블을 만들어 link에 있는 row들을 다 옮겨보자.
```
CREATE TABLE link_Tmp (LIKE link);

INSERT INTO link_tmp 
SELECT
   *
FROM
   link
WHERE
   last_update IS NOT NULL;
```

```
SELECT
   *
FROM
   link_tmp;
```

<img src="https://sp.postgresqltutorial.com/wp-content/uploads/2013/06/PostgreSQL-insert-into-select-example.png">

### Get the last insert id

```
INSERT INTO link (url, NAME, last_update)
VALUES('http://www.postgresql.org','PostgreSQL',DEFAULT) 
RETURNING id;
```

마지막에 사용된 RETURNING은 PostgreSQL의 확장으로 삽입하며 마지막으로 삽입된 id 값을 반환해준다.

<img src="https://sp.postgresqltutorial.com/wp-content/uploads/2013/06/PostgreSQL-last-insert-id.png">

# UPDATE

테이블의 열(cloumn)값을 바꾸기 위해선 UPDATE 문이 필요하다.
```
UPDATE table
SET column1 = value1,
    column2 = value2,...
WHERE
    condition;
```

> UPDATE절의 table 이름을 명시하고, SET절에서 바꾸고자 하는 열의 목록을 넣는다. 만약 여러 열을 동시에 바꾸자하면 comma(,)를 넣어서 이어가자.
마지막으로 WHERE 절에서 업데이트를 원하는 행(rows)의 조건은 정해라. WHERE 절이 없다면 모든 행이 바뀌게 될 것이다.

* PostgreSQL UPDATE 예시

INSERT에서 사용한 테이블을 업데이트 해보자. 조건은 last_update가 NULL 상태인 애들을 바꾸는 것이다.

```
UPDATE link
SET last_update = DEFAULT
WHERE
   last_update IS NULL;
```

<img src="https://sp.postgresqltutorial.com/wp-content/uploads/2013/06/PostgreSQL-update-table-partially.png">

* PostgreSQL update with returning

앞서 INSERT에서 사용한 RETURNING은 UPDATE에서도 사용 할 수 있다.

```
UPDATE link
SET description = 'Learn PostgreSQL fast and easy',
 rel = 'follow'
WHERE
   ID = 1 
RETURNING id,
   description,
   rel;
```

update 된것을 확실하게 하기 위해선 select 에서 보자.

```
SELECT 
    *
FROM
    link
WHERE
    ID = 1;
```

<img src="https://sp.postgresqltutorial.com/wp-content/uploads/2013/06/PostgreSQL-link-table-with-id-1.png">

# PostgreSQL UPDATE Join with A Practical Example

때때로 다른 테이블의 값을 가져와 UPDATE를 해야 하는 경우도 있다. 이 경우 **UPDATE join** 를 사용하면 된다.

```
UPDATE A
SET A.c1 = expression
FROM B
WHERE A.c2 = B.c2;
```

UPDATE 문에서 join 을 하고 싶으면, FROM절에서 join 할 테이블을 명시하고 WHERE절에서 join할 조건을 정한다. FROM 절은 SET 절 바로 뒤에 와야 한다.

* PostgreSQL UPDATE JOIN 예시

<img src="https://sp.postgresqltutorial.com/wp-content/uploads/2017/03/PostgreSQL-UPDATE-JOIN-Sample-Database.png">

위의 테이블을 가지고 예시를 들어보자. UPDATE JOIN이 유용하게 쓰이는 경우는 이때다.

```
UPDATE product
SET net_price = price - price * discount
FROM
product_segement
WHERE
product.segement_id = product_segment.id;
```

net_price를 product에 없는 product_Segement 테이블에 미리 정해진 값인 discount를 사용하여 변경 하는 것이다.

#PostgreSQL DELETE

테이블로부터 데이터를 삭제하기 위해선 DELETE를 사용한다.

```
DELETE FROM table
WHERE condition;
```
> 첫째, DELETE FROM에서 삭제할 데이터가 있는 테이블을 알려주고 두번째로 어떤 행을 지울지 WHERE절에서 조건문을 만들어 준다. UPDATE와 같이
WHERE 절을 생략하면 모든 행을 삭제한다.

만약 다른 테이블에 있는 열을 가지고 조건을 만들고 싶다면 USING 을 쓰자.

```
DELETE FROM table
USING another_table
WHERE table.id = another_table.id AND ...
```

USING을 사용하기 싫다면, 서브쿼리를 사용하는 방법도 있다.

```
DELETE FROM table
WHERE table.id = (SELECT id FROM another_table);
```

* DELETE 예시

앞에서 만들고 사용한 link, link_tmp 테이블을 사용해본다.

```
DELETE FROM link 
USING link_tmp or USING 생략 후 서브쿼리
WHERE
    link.id = link_tmp.id; or USING 생략 후 서브쿼리 (SELECT id FROM link_tmp)
```

# PostgreSQL Upsert Using INERT ON CONFLICT statment

관계형 데이터베이스에서 upsert는 merge를 의미한다. 이 아이디어는 새로운 행을 테이블에 insert 했을때, PostgreSQL은 기존에 있는 행을 UPDATE 하거나
 새로운 행을 INSERT한다. 이것이 upsert(update or insert)의 의미다.
 
upsert를 사용하기 위해선, **INSERT ON CONFLICT** 를 사용해야 한다. 

```
INSERT INTO table_name(column_list) VALUES(value_list)
ON CONFLICT target action;
```

PostgreSQL 에서는 INSERT문에서 upsert하는 것을 지원하기 위해서 **ON CONFLICT target action** 절을 추가했다.

* target 에 들어올 수 있는 것들
  * (column_name) : 열 이름
  * ON CONSTRAIN constraint_name : constraint_name 이 UNIQUE 인 경우를 파악,
  * WHERE predicate : 
  
* action 에 들어 갈 수 있는 것들
  * DO NOTHING : 테이블의 행에 이미 존재하면 아무것도 하지 않는다. 
  * DO UPDATE SET column_1 = value_1, .. WHERE condtion : 일부 테이블 속의 fields 를 업데이트 함
  
* PostgreSQL upsert 예시

'Microsoft'의 메일을 'contact@microsoft.com' 을 'hotline@microsoft.com'으로 바꾸는 것은 INSERT ON CONFLICT를 사용하면 가능하다.(UPDATE도 물론
가능 하지만, 된다는걸 보여주기 위해서)

우선 customers 테이블을 만들자.

```
CREATE TABLE customers (
   customer_id serial PRIMARY KEY,
   name VARCHAR UNIQUE,
   email VARCHAR NOT NULL,
   active bool NOT NULL DEAFULT TRUE
);
```

여기서 name 이 UNIQUE인 것을 주목해야 한다.

```
INSERT INTO customers (NAME, email)
VALUES
   ('IBM', 'contact@ibm.com'),
   (
      'Microsoft',
      'contact@microsoft.com'
   ),
   (
      'Intel',
      'contact@intel.com'
   );
```
여기서 Microsoft의 이메일 값을 바꿔보자.

```
INSERT INTO customers (NAME, email)
VALUES
   (
      'Microsoft',
      'hotline@microsoft.com'
   ) 
ON CONFLICT ON CONSTRAINT customers_name_key 
DO NOTHING;
```

기존에 MIcrosoft 라는 중복값이 존재해(ON CONFLICT ON CONSTRAINT ... )서 DO NOTHING 이 실행된다. 결과적으로 아무런 변화가 없다.

```
INSERT INTO customers (name, email)
VALUES
   (
      'Microsoft',
      'hotline@microsoft.com'
   ) 
ON CONFLICT (name) 
DO NOTHING;
```

위와 마찬가지로, 기존에 존재하는 이름으로(ON CONFLICT (name)) DO NOTHING이 실행된다. 이런 상황을 피하기 위해선 UPDATE문을 사용해야 한다.

```
INSERT INTO customers (name, email)
VALUES
   (
      'Microsoft',
      'hotline@microsoft.com'
   ) 
ON CONFLICT (name) 
DO
      UPDATE
     SET email = EXCLUDED.email || ';' || customers.email;
```

* EXCLUDED.email : INSERT를 시도하는 데이터
* customers.email : 기존에 저장된 데이터
