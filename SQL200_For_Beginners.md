## 초보자를 위한 SQL 200제



### 001 특정 열(COLUMN) 선택하기

```sql
SELECT empno, ename, sal
	FROM emp;
```

> 가독성을 위해 SQL은 대문자, 컬럼명과 테이블명은 소문자로 작성
>
> 한 줄에 써도 되지만 SQL문별로 줄바꿈 및 들여쓰기 권장 > 길어지는 SQL 대비



### 002 모든 열(COLUMN) 출력하기

```sql
SELECT *
	FROM emp;
```

- 특정 컬럼 한번 더 출력하고 싶을 때

  ```sql
  SELECT emp.*, empno
  	FROM emp;
  ```

  > `* ` 앞에 테이블명 쓰고 원하는 컬럼 추가하기

   

### 003 컬럼 별칭을 사용하여 출력되는 컬럼명 변경하기

```sql
SELECT empno as 사원번호, ename as 사원이름, sal as "Salary"
	FROM emp;
```

> `사원이름` 을 `사원 이름` 으로 띄워쓰면 에러 뜸
>
> `"Salary"` 로 따옴표를 써주면 대소문자 구분 / 그냥 `Salary`로 쓰면 컬럼명 `SALARY` 로 나옴



### 004 연결 연산자 사용하기(||)

```sql
SELECT ename || sal
    FROM emp;

SELECT ename || '의 월급은' || sal || '입니다' as 월급정보
    FROM emp;
```



### 005 중복된 데이터를 제거해서 출력하기(DISTINCT)

```sql
SELECT DISTINCT job
    FROM emp;
    
SELECT UNIQUE job
    FROM emp;
```

> 둘이 실행결과 같음



### 006 데이터를 정렬해서 출력하기(ORDER BY)

```sql
SELECT ename, sal
    FROM emp
    ORDER BY sal ASC;
    
# 별칭 사용해서 정렬하기
SELECT ename, sal as 월급
    FROM emp
    ORDER BY 월급 ASC;

# 여러 기준으로 정렬하기
SELECT ename, deptno, sal
    FROM emp
    ORDER BY deptno ASC, sal DESC;	# 먼저 부서 번호 오름차순으로 정렬하고, 부서 번호같으면 월급 높은 순으로 정렬
    
# ORDER BY절에 컬럼명 대신 숫자 적기
SELECT ename, deptno, sal
    FROM emp
    ORDER BY 2 ASC, 3 DESC;
```



### 007 WHERE절 배우기 - 1 (숫자 데이터 검색)

```sql
SELECT ename as 이름, sal as 월급, job as 직업
    FROM emp
    WHERE sal >= 3000;

# WHERE절에 별칭 사용 -> 에러
SELECT ename as 이름, sal as 월급, job as 직업	# 실행순서 3
    FROM emp	# 실행순서 1
    WHERE 월급 >= 3000;	# 실행순서 2 (아직 '월급' 정의되기 전)
```



### 008 WHERE절 배우기 - 2 (문자와 날짜 검색)

```sql
# 사원 이름으로 제한
SELECT ename, sal, job, hiredate, deptno
    FROM emp
    WHERE ename = 'SCOTT';

# 날짜로 제한하기 전에 현재 접속한 세션의 날짜 형식 확인하기
SELECT *
    FROM NLS_SESSION_PARAMETERS
    WHERE PARAMETER = 'NLS_DATE_FORMAT';

# 입사일로 제한
SELECT ename, sal, job, hiredate, deptno
    FROM emp
    WHERE hiredate = '81/11/17';	# RR/MM/DD 형식
```

> 접속한 세션 파라미터 변경 `ALTER SESSION SET NLS_DATE_FORMAT = 'YY/MM/DD';`



### 009 산술 연산자 배우기 (*, /, +, -)

```sql
SELECT ename as 직원, sal * 12 as 연봉
    FROM emp
    WHERE sal*12 >= 36000;

# 커미션 없으면 NULL 출력 > 총액도 NULL로 출력
SELECT ename as 직원, sal as 월급, comm as 커미션, sal+comm as 총액
    FROM emp
    WHERE deptno = 10;

# 없으면 NULL로 나오는 문제 해결 > 기본값 0 세팅
SELECT ename as 직원, sal as 월급, NVL(comm, 0) as 커미션, sal+NVL(comm, 0) as 총액
    FROM emp
    WHERE deptno = 10;
```



### 010 비교 연산자 배우기 - 1 (>, <, >=, <=, =, !=, <>, ^=)

```sql
SELECT ename, sal, job, deptno
	FROM emp
	WHERE sal <= 1200;
```



### 011 비교 연산자 배우기 - 2 (BETWEEN AND)

```sql
SELECT ename, sal
	FROM emp
	WHERE sal BETWEEN 1000 AND 3000;
# 같은 표현
SELECT ename, sal
	FROM emp
	WHERE (sal >= 1000 AND sal <= 3000);   
	
SELECT ename, sal
	FROM emp
	WHERE sal NOT BETWEEN 1000 AND 3000;
# 같은 표현
SELECT ename, sal
	FROM emp
	WHERE (sal < 1000 OR sal > 3000); 

# 날짜 적용
SELECT ename, hiredate
	FROM emp
	WHERE hiredate BETWEEN '82/01/01' AND '82/12/31';
```

❗주의 : `AND` 의 앞에 `하한값`, 뒤에 `상한값`을 넣어야 하며, 순서가 바뀔 경우 검색되지 않음.

> ex. `BETWEEN 3000 AND 1000`

✔ 여러 방법이 있지만 `BETWEEN AND` 사용하는 것이 가독성이 뛰어나고 코드가 심플함



### 012 비교 연산자 배우기 - 3 (LIKE)

```sql
SELECT ename
	FROM emp
	WHERE ename LIKE 'S%';	# 첫 글자가 S로 시작
	
SELECT ename
	FROM emp
	WHERE ename LIKE '_M%';	# 두 번째 글자가 M

SELECT ename
	FROM emp
	WHERE ename LIKE '%T';	# T로 끝남

SELECT ename
	FROM emp
	WHERE ename LIKE '%A%';	# A를 포함
```



### 013 비교 연산자 배우기 - 4 (IS NULL)

```sql
SELECT ename as 이름, comm as 커미션
	FROM emp
	WHERE comm is null;
```

> NULL은 `알 수 없는 값` 이기 때문에 `=` 이나 `!=` 로 비교할 수 없음. 반드시 `is null` 을 사용해야 함.



### 014 비교 연산자 배우기 - 5 (IN)

```sql
SELECT ename, sal, job
	FROM emp
	WHERE job IN ('SALESMAN', 'ANALYST', 'MANAGER');

# 포함 안되는 값 // NOT IN
SELECT ename, sal, job
	FROM emp
	WHERE job NOT IN ('SALESMAN', 'ANALYST', 'MANAGER');
```

> 리스트 값에 반드시 따옴표 `''` 해주기!!



### 015 논리 연산자 배우기 (AND, OR, NOT)

```sql
SELECT ename, sal, job
    FROM emp
    WHERE job = 'SALESMAN' AND sal >= 1200;
```

