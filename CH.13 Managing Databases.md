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

# PostgreSQL ALTER DATABASE

새로 만든 데이터베이스는 **ALTER DATABASE** 를 사용해서 features들을 바꿀 수 있다.

```
ALTER DATABASE target_database action;
```

ALTER DATABASE 뒤에 변경하고 싶은 데이터베이스 이름을 적으면 된다. PostgreSQL은 action 에서 다양한 옵션을 제공한다.

* Rename database

데이터베이스의 이름을 바꾸기 위해선, **ALTER DATABASE RENAME TO** 를 사용하면 된다.
```
ALTER DATABASE target_database RENAME TO new_database;
```
데이터베이스의 이름을 바꾸기 위해선, 다른 데이터베이스와 연결되어 있어야 한다.

* Change owner

데이터베이스의 owner를 바꾸기 위해선 **ALTER DATABASE OWNER TO** 를 사용하면 된다.
```
ALTER DATABASE target_database OWNER TO new_onwer;
```
오직 superuser 나 데이터베이스의 owner 만이 데이터베이스의 owner가 될 수 있다. 데이터베이스의 owner는 또한 데이터베이스의 이름을 바꿀 수
있는 CREATEDB 권한이 있어야 한다.

* Change tablespace

데이터베이스의 기본 tablespace를 바꾸기 위해선 **ALTER DATABASE SET TABLESPACE** 를 사용하면 된다.
```
ALTER DATABASE target_database SET TABLESPACE new_tablespace;
```

실행 후 테이블과 인덱스를 기존의 tablespace에서 새로 지정된 tablespace로 이동시킨다.

* 런타임 구성 변수(Configration variables) 변경

데이터베이스에 접속할 때마다, PostgreSQL은 postgresql.conf 파일에 표시된 구성 변수를 로드하고 기본적으로 이러한 변수들을 사용한다.

현재 데이터베이스의 세팅값을 변경하기 위해선, **ALTER DATABASE SET** 을 사용하면 된다.

```
ALTER DATABASE target_database SET configuration_parameter = value;
```

후속 세션에서는 PostgreSQL에서 postgresql.conf의 세팅값을 덮어씌운다. 이것은 superuser나 database owner 만이 바꿀 수 있다.


* PostgreSQL ALTER DATABASE example

> testdb2 라는 새로운 데이터베이스를 만들자.
```
CREATE DATABASE testdb2;
```

> 두번째로, testdb2의 이름을 testhrdb로 바꾼다.
```
ALTER DATABASE testdb2 RENAME TO testhrdb;
```

> 세번째로, testhrdb의 owner를 hr로 변경한다. 만약 hr이 없다면

```
CREATE ROLE hr
 VALID UNTIL 'infinity';
```
를 실행해 role 을 만들고 owner 를 변경한다.

```
ALTER DATABASE testhrdb OWNER TO hr;
```

> 네번째로는, testhrdb의 테이블스페이스를 pg_default에서 hr_default로 변경한다. 마찬가지로 hr_default가 없다면 만들어주자.
```
CREATE TABLESPACE hr_default
 OWNER hr
 LOCATION E'C:\\pgdata\\hr';
```
그리고 실행
```
ALTER DATABASE testhrdb
SET TABLESPACE hr_default;
```

> 마지막으로 esapce_string_warning 구성변수를 off로 바꿔준다.
```
ALTER DATABASE testhrdb SET esapce_string_warning TO off;
```

## PostgreSQL Rename Database: A Quick Guide

PostgreSQL의 이름을 바꾸기 위해선 이런 과정을 거쳐야 한다.

1. 다른 데이터베이스에 연결함으로, 이름을 바꾸고자 하는 데이터베이스와의 접속을 끊는다.
2. 이름을 바꿀 데이터베이스에 대한 모든 활성화된 연결을 확인하고 종료한다.
3. 데이터베이스의 이름을 바꾸는 ALTER DATABASE 문을 사용한다.

이름을 바꿀 데이터베이스를 'db'라고 가정하고 진행하자.

```
CREATE DATABASE db;
```

db의 이름을 newdb로 바꾸기 위해선, 이런 과정을 거쳐야 한다.

> 첫째로, postgres 라는 데이터베이스의 이름으로 바꾸고 싶다면, postgres와의 연결을 끊어야 한다.
```
db=# \conntect postgres;
```
다른 데이터베이스에 접속함으로서, 자동으로 연결되어 있는 데이터베이스와의 연결이 끊긴다.

