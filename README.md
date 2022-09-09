## Oracle Database 학습 내용



### 1. Oracle DB에 접속

```sql
> sqlplus
> Enter user-name: sys AS SYSDBA >> 시스템 DB 관리자 계정으로 로그인
```



### 2. 유저 생성

```sql
> CREATE USER bruno IDENTIFIED BY [password];
```

> ORA-65096 에러 발생

```sql
ALTER SESSION SET "_ORACLE_SCRIPT" = TRUE;
다시 CREATE USER bruno IDENTIFIED BY [password];
```

유저 생성 완료
But, 아직 권한 없으므로 로그인 안됨

```sql
> exit
> Enter user-name: sys AS SYSDBA
> GRANT DBA TO bruno;
```

원래 DBA는 최대한 권한을 많이 주는 것이라 실무에서는 사용자에게 이렇게 큰 권한 줄 경우는 없음.

실습의 편의를 위해 준 것임

### ❗❗❗중요

```sql
> commit;	// 모든 변경사항 뒤에는 커밋 필수!! 나 뿐만 아니라 DB에 접속하는 다른 사람도 볼 수 있게!
```



### 3. Table 생성

```sql
CREATE TABLE topic (
	id NUMBER NOT NULL,
	title VARCHAR2(50) NOT NULL,
	description VARCHAR2(4000),		// 비어도 되는 값 NULL or 비워두기
	created DATE NOT NULL
);
```

- Table 목록 조회 - Table 제목으로

```sql
SELECT table_name FROM all_tables WHERE OWNER = 'BRUNO';
> 출력
TABLE_NAME
--------------------------------------------------------------------------------
TOPIC
```



### 4. CRUD

1) Create

   ```sql
   INSERT INTO topic
   	(id,title,description,created)
   	VALUES
   	(1,'ORACLE','ORACLE is...', SYSDATE);
   
   INSERT INTO topic
   	(id,title,description,created)
   	VALUES
   	(2,'MySQL', NULL, SYSDATE);
   
   INSERT INTO topic
   	(id,title,description,created)
   	VALUES
   	(3,'SEUNGWON','Welcome New Crew!!', SYSDATE);
   ```

2. Read

   ```sql
   SELECT * FROM topic;
   
   SELECT id, title, created FROM topic;
   
   SELECT * FROM topic WHERE id = 1;
   
   SELECT * FROM topic WHERE id > 1;
   
   SELECT id, title FROM topic WHERE id = 3;
   
   SELECT * FROM topic ORDER BY id DESC;	// 내림차순
   SELECT * FROM topic ORDER BY id ASC;	// 오름차순
   
   SELECT * FROM topic
   	OFFSET 1 ROWS;	// 어디부터 가져올 것인가? 1개 뒤의 것부터
   
   SELECT * FROM topic
   	OFFSET 1 ROWS
   	FETCH NEXT 2 ROWS ONLY;	// FETCH -> 몇 개를 가져올 것인가? 하나의 페이지가 몇 개의 행을 갖는가? 1개
   ```

3. Update

   ```sql
   UPDATE topic
   	SET
   	title = 'Hmsec',	// 콤마 필수
   	description = 'Hmsec is good!'
   	WHERE	// Update나 Delete에서 WHERE문이 매우 중요!!
   	id = 2;
   ```

4. Delete

   ```sql
   DELETE FROM topic WHERE id = 1;
   
   DROP TABLE topic;	// 테이블 전체 삭제 > 커밋 필요 x
   ```



### 5. Primary Key

```sql
CREATE TABLE topic (
	id NUMBER NOT NULL,
	title VARCHAR2(50) NOT NULL,
	description VARCHAR2(4000),
	created DATE NOT NULL,
	CONSTRAINT PK_TOPIC PRIMARY KEY(id)
);

INSERT INTO topic
	(id,title,description,created)
	VALUES
	(1,'ORACLE','ORACLE is...', SYSDATE);

INSERT INTO topic
	(id,title,description,created)
	VALUES
	(1,'MySQL', NULL, SYSDATE);	// 무결성 조건에 걸림 id중복

INSERT INTO topic
	(id,title,description,created)
	VALUES
	(3,'SEUNGWON','Welcome New Crew!!', SYSDATE);

>>PRIMARY KEY를 지정했는지에 따라 아래 명령어 수행 속도 차이는 매우매우 큼
>>지정하면 매우 빠름
SELECT id, title FROM topic WHERE id = 2;
```



### 6. Sequence

Table에 새로운 row 추가할 때...

```sql
INSERT INTO topic
	(id,title,description,created)
	VALUES
	(4,'Hmsec','Welcome Hmsec!!', SYSDATE);
```

이렇게 매번 기존 row의 id 최댓값을 찾고, 1 늘려준 id 값으로 새로 INSERT 하는건 너무 귀찮다..

간편한 방법이 없을까? >> 에 대한 해답이 `SEQUENCE`

```sql
CREATE SEQUENCE SEQ_TOPIC;
INSERT INTO topic
	(id,title,description,created)
	VALUES
	(SEQ_TOPIC.NEXTVAL,'Hmsec','Welcome Hmsec!!', SYSDATE);
```

이렇게 입력하면, SEQ_TOPIC이 처음에 0이었다가 NEXTVAL에 의해 1씩 증가해서 id도 1이 됨

(아무것도 없는 Table에 첫 row데이터를 넣을 때 기준)

그래서 `4. CRUD > 1. Create` 에서 입력한 INSERT문을 다시 입력하면 아래와 같이 할 수 있다.

```sql
INSERT INTO topic
	(id,title,description,created)
	VALUES
	(SEQ_TOPIC.NEXTVAL,'ORACLE','ORACLE is...', SYSDATE);

INSERT INTO topic
	(id,title,description,created)
	VALUES
	(SEQ_TOPIC.NEXTVAL,'MySQL', NULL, SYSDATE);

INSERT INTO topic
	(id,title,description,created)
	VALUES
	(SEQ_TOPIC.NEXTVAL,'SEUNGWON','Welcome New Crew!!', SYSDATE);
```



### 7. 현재 SEQUENCE값 조회

```sql
SELECT SEQ_TOPIC.CURRVAL FROM topic;
> 출력
   CURRVAL
----------
        3
        3
        3
        3
SELECT SEQ_TOPIC.CURRVAL FROM DUAL;	// DUAL : 가상의 표. 시퀀스 값 하나만 보여줌
> 출력
   CURRVAL
----------
        3
```



### 8. Table의 분해 조립

1. Table의 분해

   | topic |          |                    |          |       |           |
   | ----- | -------- | ------------------ | -------- | ----- | --------- |
   | id    | title    | description        | created  | name  | profile   |
   | 1     | ORACLE   | ORACLE is ...      | 22/09/01 | bruno | developer |
   | 2     | MySQL    |                    | 22/09/01 | bruno | developer |
   | 3     | SEUNGWON | Welcome New Crew!! | 22/09/01 | jack  | DBA       |

이렇게 Table을 생성하면, `bruno`의 `profile`을 변경할 때 1억번 일일이 변경해야 함 > 매우 비효율적

또한, 같은 `developer`라는 직책의 동명이인 `bruno` 가 있는 경우 구분할 수 없음

게다가, 구성원으로서 존재하지만 글은 작성하지 않은 `jack`은 처리할 수 없음

이와 같은 문제를 해결하기 위해 `author`라는 Table을 따로 분리하는 것임

| topic |          |                    |          |           |
| ----- | -------- | ------------------ | -------- | --------- |
| id    | title    | description        | created  | author_id |
| 1     | ORACLE   | ORACLE is ...      | 22/09/01 | 1         |
| 2     | MySQL    |                    | 22/09/01 | 1         |
| 3     | SEUNGWON | Welcome New Crew!! | 22/09/01 | 2         |

| author |       |                |                                      |
| ------ | ----- | -------------- | ------------------------------------ |
| id     | name  | profile        |                                      |
| 1      | bruno | developer      |                                      |
| 2      | jack  | DBA            |                                      |
| 3      | jenny | data scientist | 존재만 하고 글 작성하지 않은 자 처리 |
| 4      | bruno | developer      | 동명이인 처리                        |

이렇게 Table을 두 개로 분리하면, `author` 테이블에서 `profile`을 수정하면, `topic` 에서 해당 `author_id` 와 연동된 부분이 자동으로 한 번에 바뀌는 효과가 생김 >> 매우 효율적!!

> But!! 이렇게 분해하면 효율적인 대신 가독성이 떨어짐..



2. Table의 조립 - `JOIN`

   분해한 Table의 가독성을 높이기 위해 연관 있는 행끼리 붙여 가상의 Table을 생성하는 것이  JOIN이다.

   ```sql
   SELECT * FROM topic LEFT JOIN author ON topic.author_id = author.id;
   ```

   `LEFT JOIN` : 왼쪽 테이블을 기준으로 오른쪽에 연결할 Table을 붙인다.

   `topic.author_id = author.id` : `topic` 테이블의 `author_id` 와 `author` 테이블의 `id` 값이 같은 것끼리 붙인다.

| topic |          |                    |          |           |      |       |           |
| ----- | -------- | ------------------ | -------- | --------- | ---- | ----- | --------- |
| id    | title    | description        | created  | author_id | id   | name  | profile   |
| 1     | ORACLE   | ORACLE is ...      | 22/09/01 | 1         | 1    | bruno | developer |
| 2     | MySQL    |                    | 22/09/01 | 1         | 1    | bruno | developer |
| 3     | SEUNGWON | Welcome New Crew!! | 22/09/01 | 2         | 2    | jack  | DBA       |

여기서, id행이 2개이므로 다른 팀원이 볼 때 헷갈릴 수 있음. 아래와 같이 sql문을 실행하면,

```sql
SELECT 
    T.id TOPIC_ID, 	// 뒤의 TOPIC_ID는 별명
    title, 
    name 
FROM topic T	// topic 테이블을 T라고 부르겠다.
    LEFT JOIN author A	// author 테이블을 A라고 부르겠다.
    ON T.author_id = A.id
;
```

이렇게 바뀜.

| TOPIC_ID | TITLE    | NAME  |
| -------- | -------- | ----- |
| 1        | ORACLE   | bruno |
| 2        | MySQL    | bruno |
| 3        | SEUNGWON | jack  |

