# Import CSV File Into PostgreSQL Table

우선 카피 받아올 csv파일의 열에 맞게 테이블을 만들어놓는다.

```
CREATE TABLE persons
(
  id serial NOT NULL,
  first_name character varying(50),
  last_name character varying(50),
  dob date,
  email character varying(255),
  CONSTRAINT persons_pkey PRIMARY KEY (id)
)
```

이후 여러가지 방법으로 CSV 파일을 만든다.

<img src="https://sp.postgresqltutorial.com/wp-content/uploads/2015/05/csv-file.jpg">

이제 PostgreSQL 에서 쿼리문으로 카피해오자. 이후 persons 테이블을 확인해보면 정상적으로 카피해온 것을 볼 수 있다.

```
COPY persons(first_name,last_name,dob,email) 
FROM 'C:\tmp\persons.csv' DELIMITER ',' CSV HEADER;
```

복사해오는 과정을 자세히 알아본다면,

> 첫째, 복사할 테이블과 칼럼(열)의 이름을 COPY 다음에 이어지게 하자. 열의 순서는 CSV파일과 같아야 한다. 만약 CSV 파일의 모든 열을 가져온다면
굳이 열을 명시할 필요가 없다.

```
COPY sample_table # 이상태처럼 명시할 필요가 없음
FROM 'C:\tmp\sample_data.csv' DELIMITER ',' CSV HEADER;
```

> 둘째, FROM 이후 CSV파일의 경로를 지정하고 DELIMITER를 CSV파일을 사용할땐 같이 써줘야 한다.
> 셋째, HEADER 키워드는 CSV파일의 첫번째 행이 열이라는 것을 알려준다. 이를 명시할 경우 Postgre는 첫번째 행을 무시하고 두번째 행부터
입력한다.

파일은 SQL서버에서 직접 읽어야 한다. 따라서 PostgreSQL에서 접속할 수 있어야 하는데 SQL 서버 시스템 또한 상위 사용자 권한이 있는 경우, COPY 문을
성공적으로 실행할 수 있다.

## pgAdmin 으로 csv 파일 가져오기

자세한 사항은 https://www.postgresqltutorial.com/import-csv-file-into-posgresql-table/ 참조.


-----

# Export PostgreSQL Table To CSV File

테이블을 csv 파일로 내보내는 것은 위에서 사용한 COPY 를 사용하면 간편하다.

```
COPY persons TO 'C:\tmp\persons_db.csv' DELIMITER ',' CSV HEADER;

# 만약 특정 열만 내보내고 싶다면,
COPY persons(first_name,last_name,email) 
TO 'C:\tmp\persons_partial_db.csv' DELIMITER ',' CSV HEADER;

# 헤더는 복사하지 않고, 데이터만 복사하고 싶다면 'HEDAER' 없이 사용하면 된다.
COPY persons(email) 
TO 'C:\tmp\persons_email_db.csv' DELIMITER ',' CSV;

```

### \copy 커맨드를 사용한 테이블을 csv파일로 내보내기

원격 PostgreSQL 데이터 베이스 서버(remote PostgreSQL database server) 에서 접속 할 때는, 파일에 쓸 수 있는 권한이 없으므로 **\copy** 명령어를 
사용해야 한다.

\copy 는 위의 copy 문을 기본적으로 실행한다. 하지만 psql이 서버 대신 csv파일을 작성하고 서버에서 로컬 파일 시스템으로 데이터를 전송한다.
\copy 명령어를 사용하기 위해선 로컬 컴퓨터에 대한 권한이 필요하다. PostgreSQL superuser 권한은 필요하지 않다.

```
\copy (SELECT * FROM persons) to 'C:\tmp\persons_client.csv' with csv
```
persons 테이블의 내용을 persons_client.csv 파일에 내보내고 싶다면 위와 같은 식을 사용하면 된다.
