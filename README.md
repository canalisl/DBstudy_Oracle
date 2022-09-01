## Oracle Database 학습 내용



### 1. Oracle DB에 접속

```shell
> sqlplus
> Enter user-name: sys AS SYSDBA >> 시스템 DB 관리자 계정으로 로그인
```



### 2. 유저 생성

```shell
> CREATE USER bruno IDENTIFIED BY [password];
```

> ORA-65096 에러 발생

```
ALTER SESSION SET "_ORACLE_SCRIPT" = TRUE;
다시 CREATE USER bruno IDENTIFIED BY [password];
```

유저 생성 완료
But, 아직 권한 없으므로 로그인 안됨

```shell
> exit
> Enter user-name: sys AS SYSDBA
> GRANT DBA TO bruno;
```

원래 DBA는 최대한 권한을 많이 주는 것이라 실무에서는 사용자에게 이렇게 큰 권한 줄 경우는 없음.

실습의 편의를 위해 준 것임

### ❗❗❗중요

```shell
> commit;	// 모든 변경사항 뒤에는 커밋 필수!! 나 뿐만 아니라 DB에 접속하는 다른 사람도 볼 수 있게!
```



### 3. Table 생성

```shell
CREATE TABLE topic (
	id NUMBER NOT NULL,
	title VARCHAR2(50) NOT NULL,
	description VARCHAR2(4000),		// 비어도 되는 값 NULL or 비워두기
	created DATE NOT NULL
);
```

- Table 목록 조회 - Table 제목으로

```shell
SELECT table_name FROM all_tables WHERE OWNER = 'BRUNO';
> 출력
TABLE_NAME
--------------------------------------------------------------------------------
TOPIC
```



### 4. CRUD

1) Create

   ```shell
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

   ```shell
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

   ```shell
   UPDATE topic
   	SET
   	title = 'Hmsec',	// 콤마 필수
   	description = 'Hmsec is good!'
   	WHERE	// Update나 Delete에서 WHERE문이 매우 중요!!
   	id = 2;
   ```

4. Delete

   ```shell
   DELETE FROM topic WHERE id = 1;
   
   DROP TABLE topic;	// 테이블 전체 삭제 > 커밋 필요 x
   ```



### 5. Primary Key

```shell
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

```shell
INSERT INTO topic
	(id,title,description,created)
	VALUES
	(4,'Hmsec','Welcome Hmsec!!', SYSDATE);
```

이렇게 매번 기존 row의 id 최댓값을 찾고, 1 늘려준 id 값으로 새로 INSERT 하는건 너무 귀찮다..

간편한 방법이 없을까? >> 에 대한 해답이 `SEQUENCE`

```shell
CREATE SEQUENCE SEQ_TOPIC;
INSERT INTO topic
	(id,title,description,created)
	VALUES
	(SEQ_TOPIC.NEXTVAL,'Hmsec','Welcome Hmsec!!', SYSDATE);
```

이렇게 입력하면, SEQ_TOPIC이 처음에 0이었다가 NEXTVAL에 의해 1씩 증가해서 id도 1이 됨

(아무것도 없는 Table에 첫 row데이터를 넣을 때 기준)

그래서 `4. CRUD > 1. Create` 에서 입력한 INSERT문을 다시 입력하면 아래와 같이 할 수 있다.

```shell
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

```shell
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

