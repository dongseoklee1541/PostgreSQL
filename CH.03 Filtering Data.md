# PostgreSQL WHERE

쿼리문에서 조건을 넣고 싶다면 "WHERE"을 사용하면 된다.

```
SELECT select_list
FROM table_name
WHERE condition;
```
WHERE 문은 FROM 문 다음에 사용해야 한다. WHERE의 조건문은 true,false 또는 unknown이 되어야 한다. 쿼리문은 WHERE 절의 조건에 true 인 행만 
반환한다.

<img src="https://github.com/dongseoklee1541/PostgreSQL/blob/master/image/143710.png?raw=true" width="480" hegiht="640">

* WHERE with IN Examples
테이블에 있는 문자열과 쿼리문의 문자열을 일치 시키려면 IN 을 사용하면 된다.
```
SELECT
   first_name,
   last_name
FROM
   customer
WHERE 
   first_name IN ('Ann','Anne','Annie');
```
<img src="https://www.postgresqltutorial.com/wp-content/uploads/2019/05/PostgreSQL-WHERE-with-IN-operator.png">4

* WHERE with LIKE Examples
조건에 걸리는 패턴과 일치하는 문자열을 찾으려면 LIKE를 사용하면 된다.
```
SELECT
   first_name,
   last_name
FROM
   customer
WHERE 
   first_name LIKE 'Ann%'
```
여기서 "Ann%"은 Ann 뒤에 무엇이 나오더라도 모두 받겠다는 의미로 사용 한다.

* WHERE with BETWEEN
범위 값을 주고 싶다면 BETWEEN 연산자를 사용 한다.
```
SELECT
   first_name,
   LENGTH(first_name) name_length
FROM
   customer
WHERE 
   first_name LIKE 'A%' AND
   LENGTH(first_name) BETWEEN 3 AND 5
ORDER BY
   name_length;
```

* WHERE with <>

<> condition 은 조건과 같지 않는 녀석들을 반환하는 기능이다.

```
SELECT 
   first_name, 
   last_name
FROM 
   customer 
WHERE 
   first_name LIKE 'Bra%' AND 
   last_name <> 'Motley';
```
이 조건에서는 first_name이 'Bra'로 시작하고 last_name이 'Motley'가 아닌 녀석들을 찾는다.
