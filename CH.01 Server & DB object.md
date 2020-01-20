```
pg_restore -U postgres -d dvdrental C:\dvdrental\dvdrental.tar
```
* -U postgres : pstgreSQL 서버에 접속할 유저 지정

* -d dvdrental : 로드할 DB 서버 지정

# PostgreSQL Server and Database Objects

## Server service
PostgreSQL instance를 생성할때, PostgreSQL sever (service)와 연결된다. 여러개의 서버를 서로 다른 포트를 사용하고 데이터를 저장할 위치를 다르게
만들어 생성할 수 있다.

## Database
데이터 베이스는 tables, views, functions, indexes 등의 다른 객체들의 컨테이너다. 또한 원하는 만큼의 DB서버를 postgreSQL server에 만들 수 있다.

### Tables
테이블은 데이터를 저장하는 역할을 한다. 또한 테이블은 DB에 속하고, 여러 테이블이 존재한다.

PostgreSQL의 특징으로 테이블간 상속이 가능하다는 것이다. 그렇기에 Child table(하위 테이블)에 query를 날려도 parent table(상위 테이블)의 데이터가
검색된다.

### Schemas
스키마는 DB 내부의 테이블과 기타 객체로 구성된 logical container이다. PostgreSQL에는 여러개의 스키마가 있을 수 있다. 또한 ANSI-SQL 표준의 일부다.

### Tablespaces
테이블 스페이스는 PostgreSQL이 저장되어 있는 위치다. 또한 간단한 명령을 통해서 데이터의 여러 물리적 위치를 옮길 수 있다.

기본적으로 PostgreSQL은 두개의 테이블스페이스를 제공한다.
* pg_default : 사용자 데이터를 저장하기 위한 공간
* pg_global : 시스템 데이터를 저장하기 위한 공간

### View
뷰는 데이터베이스에 저장된 쿼리다. 읽기 전용외에도 PostgreSQL은 updatable views를 제공한다.

### Functions 
함수는 재사용 가능한 SQL code 블록이다. 이것은 행 집합(a set of rows)의 스칼라 값을 반환 한다.

### Operators
연산자는 Symbolic functions이다. PostgreSQL은 사용자 지정 연산자를 정의 할 수 있다.

### Casts
Casts는 data type을 다른 data type으로 변환할 수 있다. 함수를 통해 지원되는 변환을 수행하고, PostgreSQL에서 기본적으로 제공되는 캐스트들을 
직접 생성해 override도 가능하다.

### Sequence
Sequences는 테이블에서 serial column(일련 번호)로 정의된 자동으로 증가하는(auto-increment) columns(열)을 테이블에서 관리하는데 사용된다.

### Extension
PostgreSQL은 types, casts, indexes, functions, etc. 다른 객체를 단일 단위로 wrap 하기 위한 개념이다. 목적은 유지 관리를 더 쉽게 하기 위해서 이다.

