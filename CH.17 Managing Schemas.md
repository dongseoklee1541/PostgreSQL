## PostgreSQL Schema

PostgreSQL에서 스키마는 데이터 베이스 객체(테이블,뷰,인덱스,데이터 타입, 함수 등)을 포함하는 네임스페이스다. 

스키마 객체에 접근하기 위해선 

```
schema_name.object_name
```
또는 스키마를 포함하는 경로를 찾아 설정할 수 있다. 

예를들어 sale, public 스키마는 staff 테이블을 갖고있다면 staff 테이블에 접근하기 위해서는

```
public.staff
# or
sales.staff
```

#### 왜 스키마를 써야 하는가?
* 스키마를 사용하면 데이터 베이스 객체를 논리 그룹(logical groups)으로 구성하여 관리하기 쉽다.

* 스키마를 사용하면 여러 유저가 서로 간섭 받지 않고 하나의 데이터베이스를 사용할 수 있다.

#### public 스키마

포스트그래는 데이터베이스를 만들때마다 기본적으로 public이라는 이름의 스키마를 만든다. 특정한 스키마의 이름을 설정하지 않는다면 포스트그래는
자동으로 public 스키마에 할당한다.

```
CREATE TABLE table_name(..);
# 위와 아래는 동일한 표현
CREATE TABLE public.table_name(..);
```

스키마를 명시하지 않고, 객체의 이름(예:table_name)만 사용하면 PostgreSQL은 스키마 검색 경로(schema search path)라는 것을 사용해 테이블을 검색
한다. 스키마 검색 경로는 검색할 스키마 목록이다.

포스트그래는 스키마 검색 경로의 첫번째 일치 테이블에 액세스 한다. 일치하는 항목이 없다면 오류가 반환된다. 다른 스키마에 일치하는 이름이 있더라도 말이다
.

검색 경로의 첫번째 스키마를 현재 스키마(current schema)라고 하며 스키마 이름을 명시적으로 지정하지 않고 새 객체를 만들면 현재 스키마에 저장된다.
```
SELECT current_schema(); # 현재 스키마 반환
```

현재 검색 경로를 보기 위해선, **SHOW** 를 사용하자.
```
SHOW search_path;

 search_path
-----------------
"$user", public
(1 row)
```
* $user는 현재 사용자의 이름과 같은 객체를 검색하는데 사용할 첫 번째 스키마를 지정한다. 예를 들어 postgre 라는 유저로 staff 테이블에 로그인하면
PostgreSQL은 staff 테이블은 postgre 스키마에서 찾을 것이다. 찾지 못한다면, public 스키마에서 찾는다.

```
CREATE SCHEMA sales; # 새로운 스키마 만들기

SET search_path TO sales, public; # 새로운 스키마를 검색경로에 넣기
```

위와 같이 설정했다면, 새로운 테이블을 만들때 sales 스키마에 저장될 것이다. 이때 기본 검색경로는 sales가 된다.

```
SELECT * FROM staff;
# 경로를 설정한 결과 위와 아래는 같은 결과를 나타낸다.
SELECT * FROM sales.staff;
```

다시 기본값으로 돌아가겠다면 아래와 같이 설정해주자.
```
SET search_path TO public;
```

### PostgreSQL schemas and privileges

유저는 권한이 없는 스키마에는 접근할 수 없다. 권한 없는 스키마에 접근하기 위해선 **USING** 권한을 주어야 한다.

또한 수정까지 하고 싶다면, **CREATE** 권한을 주면 된다.
```
GRANT USAGE ON SCHEMA schema_name TO user_name; # 접근권한
GRANT CREATE ON SCHEMA schema_name TO user_name; # 수정권한
```

## PostgreSQL CREATE SCHEMA

**CREATE SCHEMA** 는 현재 데이터베이스에 스키마를 만든다.

```
CREATE SCHEMA [IF NOT EXISTS] schema_name; # CREATE SCHEMA는 권한을 갖고 있어야 가능하다!
```

> 스키마의 이름은 현재 데이터베이스에서 UNIQUE 해야한다. IF NOT EXISTS는 중복에 따른 에러 회피이다.

```
CREATE SCHEMA [IF NOT EXISTS] AUTHORIZATION user_name;
```

또한 스키마를 만들면서 테이블과 뷰의 리스트를 만드는 것도 가능하다.
```
CREATE SCHEMA schema_name # 각 문장의 끝에 세미콜론이 없다는 것을 알아두자. 맨 마지막에만 들어간다.
    CREATE TABLE table_name1 (...)
    CREATE TABLE table_name2 (...)
    CREATE VIEW view_name1
        SELECT select_list FROM table_name1;
```

* 예시
https://www.postgresqltutorial.com/postgresql-create-schema/ 참조

## PostgreSQL ALTER SCHEMA

```
ALTER SCHEMA schema_name
RENAME TO new_name;
```
ALTER SCHEMA RENAME TO를 실행하기 위해선 CREATE 권한을 가지고 있어야 한다.

또한 ALTER SCHEMA는 스키마의 owner도 바꿀 수 있다.
```
ALTER SCHEMA schema_name 
OWNER TO { new_owner | CURRENT_USER | SESSION_USER};
```

## PostgreSQL DROP SCHEMA

```
DROP SCHEMA [IF EXISTS] schema_name1 [,schema_name2,...] 
[CASCADE | RESTRICT];
```

DROP SCHEMA를 실행하기 위해선, 해당 스키마의 owner가 되어야 한다.

또한 여러개의 스키마를 동시에 지울 수 있다.
