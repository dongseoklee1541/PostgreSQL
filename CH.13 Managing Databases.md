# PostgreSQL CREATE DATABASE

PostgreSQL Database를 만들기 위해선 CREATE DATABASE 문을 사용해야 한다.

```
CREATE DATABASE db_name
 OWNER =  role_name
 TEMPLATE = template
 ENCODING = encoding
 LC_COLLATE = collate
 LC_CTYPE = ctype
 TABLESPACE = tablespace_name
 CONNECTION LIMIT = max_concurrent_connection
```

* db_name : 만들고자 하는 새로운 데이터베이스의 이름이다. 데이터베이스 이름은 PostgreSQL 데이터베이스 서버에서 유일해야 한다. 만약 이미 존재하는
이름으로 데이터 베이스를 만들고자 하면, PostgreSQL는 에러를 만들것이다.

* role_name : 새로운 데이터베이스를 소유하고자 하는 유저의 이름이다. PostgreSQL은 CREATE DATABASE를 실행한 사용자를 기본적으로 role name으로
설정한다.

* template : 새로운 데이터베이스의 템플릿 이름이다. PostgreSQL은 데이터베이스를 만들때 템플릿 데이터베이스를 기반으로 만드는 것을 허용한다.
template1이 기본 템플릿 데이터베이스다.

* encoding : 새로운 데이터베이스를 위한 문자열 인코딩 방식을 명시한다. 기본값은 템플릿 데이터베이스의 인코딩을 따른다.

* collate : 새로운 데이터베이스의 데이터 정렬을 지정한다. 데이터 정렬은 SELECT 절에서 ORDER BY절에 대한 결과에 영향을 미친다. LC_COLLATE의 
매개 변수에 명시적으로 지정하지 않으면, 기본값은 템플릿 데이터베이스의 정렬 방식이다.

* ctype : 새로운 데이터베이스의 문자 분류를 지정한다. ctype은 categorization에 영향을 미친다. ex) digit, lower and upper 등..
기본값은 템플릿 데이터베이스의 방식이다.

* tablespace_name : 새로운 데이터베이스의 tablespace 이름을 지정한다. 기본값은 템플릿 데이터베이스의 방식이다.

* max_concurrent_connection : 새로운 데이터베이스의 대한 최대 동시연결을 지정한다. 기본값은 -1로 무제한이다. 이 옵션은 최대 동시연결을 지정할 수
있는 공유된 호스팅 환경에서 유용하다.


### PostgreSQL create database examples

```
CREATE DATABASE testdb1;
```

간단하게 데이터베이스를 만드는 방법이다. 위의 방식은 기본값으로 지저하는 방법이다. 데이터베이스의 이름은 **testdb1** 이고, 기본 파라미터들은
기본 템플릿 데이터베이스인 **template1** 을 따른다.

> ENCODING=UTF-8 , Owner = hr, hr유저는 데이터베이스 서버에 존재한다고 가정한다. Maximum concurrent connections : 25 라는 조건으로 테이블을
만들어 보자.

```
CREATE DATABASE hrdb
 WITH ENCODING='UTF8'
 OWNER=hr
 CONNECTION LIMIT=25;
```

### pgAdmin 을 통해서 만드는 방법

https://www.postgresqltutorial.com/postgresql-create-database/ 링크를 참조하자.

