# PostgreSQL Transaction

database Transaction이란 하나 이상의 operation으로 구성될 수 있는 단일 작업 단위이다. Transaction의 전형적인 예는 한 은행 계좌에서 다른 은행 계좌
로의 이체다. 완전한 Transaction은 송신자와 수신자의 계정에서 같은 양을 빼고 더해야한다.

PostgreSQL 의 Transaction은 ACID라고 한다.

> Atomicity(원자성) : 모든 트랜젝션(Transaction)은 완전히 실행되거나(all) 아예 실행되지 않아야 한다.(nothing) 다시 말해, 진행되다 정지한 트랜젝션은
존재할 수 없다. 이는 트랜잭션이 가장 작은 단위의 업무이기 때문이다.

> Consistency(일관성) : 같은 DB에 트랜잭션을 실행한 결과는 언제나 같아야 한다. 여러 트랜잭션을 순서대로(serial) 실행하는 것과 병렬적으로(concurrent)
실행하는 것이 같은 결과를 내야함을 강조할 때 주로 사용되는 속성이다.

> Isolation(고립성) : 모든 트랜잭션은 다른 트랜잭션에 의해 영향을 받아서는 안된다. 트랜잭션의 병렬 처리를 조정하는 concurrecy control은 주로 각
트랜잭션의 고립성 확보에 중점을 두고 있다.

> Durability(지속성, 내구성) : 성공한 트랜잭션의 결과는 안정적으로 보존되어야 한다. 원자성에 근거해 완벽히 실행된(commit) 트랜잭션은 그 결과를
비휘발성 저장장치에 저장하는 형태로 보존해야 하며, 트랜잭션을 처리하는 프로그램은 이를 보장해야 한다.

* Transaction 예시

우선 테이블부터 만들자.

```
CREATE TABLE accounts(
  id INT GENERATE BY DEFAULT AS IDENTITY,
  name VARCHAR(100) NOT NULL,
  balance DEC(15,2) NOT NULL,
  PRIMARY KEY(id)
);
```

트랜잭션을 사용하기 위해선 아래와 같은 문장으로 실행되야 한다.

```
BEGIN TRANSACTION;
 # OR
BEGIN WORK;
 # OR
BEGIN;
```

트랜잭션을 통해 INSERT를 한다면 

```
BEGIN;

INSERT INTO accounts(name,balance)
VALUES('Alice',10000);
```
위 와 같은 방식으로 하면 된다. 다만 이 경우 새로운 색션에서 실행하면 추가된 행을 보이지 않는다.

다른 세션에서도 보이게 하고 싶다면, COMMIT을 사용해야 보인다.

```
COMMIT WORK;
# OR
COMMIT TRANSACTION;
# OR
COMIT;
```

위식을 사용하여 COMMIT 을 해보자.
```
COMMIT;

SELECT
  id,
  name,
  balance
FROM
  accounts;
```

커밋을 하게 되면, 다른 세션에서도 변경사항을 볼 수 있다.

* PostgreSQL COMMIT 예시

밥의 계정에서 앨리스의 계정으로 1000USD를 보내는 방법을 해보겠다. 두 세션을 사용하여 각 작업의 변경사항을 확인하는 방식으로 진행한다.

첫번째 세션에서 새로운 거래를 시작(밥에게서 돈을 빼는 거래)

```
BEGIN;

UPDATE accounts
SET balance = balance - 1000
WHERE id = 1;
```
그리고 바로 Alice 의 계좌에 1000USD 달러를 보낸다.
```
UPDATE accounts
SET balance = balance + 1000
WHERE id = 2;
```
그러나 이런 변화는 다른 세션에서 알지 못한다. COMMIT 을 해야 이런 변화가 반영되기 때문이다. 업데이트 작업을 마친후 COMMIT을 꼭 하자.

```
COMMIT;
```

### 트랜잭션 롤 백

현재 트랜잭션의 변경 내용을 rollback 하거나 실행 취소를 하고 싶다면(COMMIT 하기전에) 다음 세가지 명령중 하나를 사용하면 된다.
```
ROLLBACK WORK;
# OR
ROLLBACK TRANSACTION;
# OR
ROLLBAK;
```

만약 밥의 계좌에서 엘리스의 계좌로 1500USD 달러를 보내고자 했지만, 실수로 다른 사람의 계좌에 돈을 보내게 된다면 어떻게 해야 할까. 이때 필요한것이
ROLLBACK이다.

```
BEGIN;

UPDATE accounts
SET balance = balance - 1500
WHERE id = 1;

UPDATE accounts
SET balance = balance + 1500
WHERE id = 3;
```
위와 같은식에서 엘리스의 id는 2인데 3에 넣어줬으므로 실수한 것이다. 이런 실수를 되돌리기 위해서는!

```
ROLLBACK;
```
이렇게 하면 BEGIN부터 실행된 UPDATE 내역이 모두 없던일로 돌아간다.


-----

출처 : https://namu.wiki/w/Acid
