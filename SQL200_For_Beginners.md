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



### 016 대소문자 변환 함수 배우기 (UPPER, LOWER, INITCAP)

```sql
SELECT UPPER(ename), LOWER(ename), INITCAP(ename)
    FROM emp;

# 이름이 대문자인지 소문자인지 확실하지 않을 때 사용
SELECT ename, sal
    FROM emp
    WHERE LOWER(ename) = 'scott';
```



### 017 문자에서 특정 철자 추출하기 (SUBSTR)

```sql
# 1번째 위치부터, 3개의 문자 선택
SELECT SUBSTR('SMITH', 1, 3)	# SMI
    FROM DUAL;

SELECT SUBSTR('SMITH', -2, 2)	# TH
    FROM DUAL;

# 2번째부터 끝까지
SELECT SUBSTR('SMITH', 2)	# MITH
    FROM DUAL;
```

> 인덱스가 아닌 그냥 위치임. 1번째는 제일 앞 문자!
>
> `DUAL` 은 1개의 `COLUMN` 과 1개의 `ROW` (더미데이터) 로 이루어진 가상의 테이블 - 함수를 실행할 때 임시로 사용하는 용도



### 018 문자열의 길이를 출력하기 (LENGTH)

```sql
SELECT ename, LENGTH(ename)
    FROM emp;
    
# 한글도 됨
SELECT LENGTH('가나다라마바사아')
    FROM DUAL;
    
# 바이트로 출력
SELECT LENGTHB('가나다라마바사아')	# 한글은 한글자에 3바이트 -> 24 출력
    FROM DUAL;
```



### 019 문자에서 특정 철자의 위치 출력하기 (INSTR)

```sql
SELECT INSTR('SMITH', 'M')
    FROM DUAL;
// 2

# 문자열에서 특정 문자열만 추출하기	abcdefg@naver.com -> naver.com 추출
SELECT SUBSTR('abcdefg@naver.com', INSTR('abcdefg@naver.com', '@') + 1)
	FROM DUAL;
	
# naver만 잘라내기
SELECT SUBSTR('abcdefg@naver.com', INSTR('abcdefg@naver.com', '@') + 1, LENGTH('naver'))
	FROM DUAL;
```



### 020 특정 철자를 다른 철자로 변경하기 (REPLACE)

```sql
SELECT ename, REPLACE(sal, 0, '*')
	FROM emp;
	
# 월급의 숫자 0~3을 *로 출력
SELECT ename, REGEXP_REPLACE(sal, '[0-3]', '*') as 월급
	FROM emp;

# 이름의 두번째 글자를 '밖'으로 출력
SELECT REPLACE(ename, SUBSTR(ename, 2, 1), '밖') as 바뀐이름
	From emp;
```



### 021 특정 철자를 N개 만큼 채우기 (LPAD, RPAD)

```sql
# 데이터 시각화하기에 좋은 쿼리
SELECT ename, LPAD(sal, 10, '$') as 왼쪽돈, RPAD(sal, 10, '*') as 오른쪽돈
    FROM emp;

SELECT ename, sal, LPAD('★', round(sal/200), '★') as bar_chart
    FROM emp;
```



### 022 특정 철자 잘라내기 (TRIM, RTRIM, LTRIM)

```sql
SELECT 'smith', LTRIM('smith', 's'), RTRIM('smith', 'h'), TRIM('s' from 'smiths')
    FROM DUAL;

# 새로운 데이터 삽입
INSERT INTO emp(empno, ename, sal, job, deptno)
    VALUES(8291, 'JACK ', 3000, 'DEVLEOPER', 30);	# 이름 옆에 공백 추가
commit;

SELECT ename, sal
    FROM emp
    WHERE ename = 'JACK';	# 아무것도 뜨지 않음 / 공백 몇개인지 모르기(가정) 때문
# 해결    
SELECT ename, sal
    FROM emp
    WHERE TRIM(ename) = 'JACK';

DELETE FROM emp
	WHERE TRIM(ename) = 'JACK';
commit;
```



### 023 반올림해서 출력하기 (ROUND)

```sql
# .의 위치가 0임
SELECT '876.567' as 숫자, ROUND(876.567, 1)	# 소수점 뒤 첫숫자 '다음'부터 반올림	876.6
    FROM DUAL;

SELECT '876.567' as 숫자, ROUND(876.567, 2)	# 876.57
    FROM DUAL;

SELECT '876.567' as 숫자, ROUND(876.567, 0)	# 877 -> 소수점 뒤 첫숫자 5(소수점 첫째자리)에서 반올림
    FROM DUAL;
# 동일
SELECT '876.567' as 숫자, ROUND(876.567)	# 877
    FROM DUAL;
    
SELECT '876.567' as 숫자, ROUND(876.567, -1)	# 880 -> 소수점 전 첫숫자(6)에서 반올림
    FROM DUAL;
    
SELECT '876.567' as 숫자, ROUND(876.567, -2)	# 900
    FROM DUAL;
```

❗ 같이 들어가는 인자가 `양수` 이거나 `0` 일때, 그 다음위치 숫자부터 반올림

❗ `음수` 일때 그 숫자부터 바로 반올림



### 024 숫자를 버리고 출력하기 (TRUNC)

```sql
SELECT '876.567' as 숫자, TRUNC(876.567, 1)	# 876.5 -> 소수점 뒤 첫숫자 '다음'부터 버림!
    FROM DUAL;
    
SELECT '876.567' as 숫자, TRUNC(876.567, -1)	# 870 -> 소수점 전 첫숫자부터 바로 버림!
    FROM DUAL;
    
SELECT '876.567' as 숫자, TRUNC(876.567)	# 876
    FROM DUAL;
```

❗ 같이 들어가는 인자가 `양수` 이거나 `0` 일때, 그 다음위치 숫자부터 버림

❗ `음수` 일때 그 숫자부터 바로 버림



### 025 나눈 나머지 값 출력하기 (MOD)

```sql
SELECT MOD(10, 3)	# 1
    FROM DUAL;
    
SELECT empno, MOD(empno, 2)
	FROM emp;
	
SELECT empno, ename
	FROM emp
	WHERE MOD(empno, 2) = 0;
	
SELECT FLOOR(10/3)	# 3.333 -> 3과 4 사이에서 제일 바닥(FLOOR)에 있는 값 출력 -> 3
    FROM DUAL;
```



### 026 날짜 간 개월 수 출력하기 (MONTHS_BETWEEN)

```sql
# 자동으로 특정날~지금 까지 기간 계산
SELECT ename, MONTHS_BETWEEN(sysdate, hiredate)
    FROM emp;

# 특정한 두 날 사이 계산
SELECT TO_DATE('2021-09-22', 'YYYY-MM-DD') - TO_DATE('2018-10-01', 'YYYY-MM-DD')
	FROM DUAL;
# 주수로 환산 / 작은 따옴표 쓰면 에러남. AS는 무조건 쌍따옴표!!
SELECT ROUND((TO_DATE('2021-09-22', 'YYYY-MM-DD') - TO_DATE('2018-10-01', 'YYYY-MM-DD')) / 7) AS "총 주수"
	FROM DUAL;
```

> RRRR과 YYYY는 차이 없음!! RR과 YY만 다른것!!



### 027 개월 수 더한 날짜 출력하기 (ADD_MONTHS)

```sql
# 100달 뒤의 날짜
SELECT ADD_MONTHS(TO_DATE('2022-09-23', 'RRRR-MM-DD'), 100)
    FROM DUAL;
# 100일 뒤의 날짜    
SELECT TO_DATE('2022-09-23', 'RRRR-MM-DD') + 100
    FROM DUAL;
# 100달 뒤의 날짜    
SELECT TO_DATE('2022-09-23', 'RRRR-MM-DD') + interval '100' month
    FROM DUAL;
# 2년 2개월 뒤의 날짜    
SELECT TO_DATE('2022-09-23', 'RRRR-MM-DD') + interval '2-2' year to month
    FROM DUAL;    
# 2년 5개월 뒤의 날짜
SELECT TO_DATE('2022-09-23', 'RRRR-MM-DD') + TO_YMINTERVAL('2-5') as 날짜
    FROM DUAL;
```



### 028 특정 날짜 뒤에 오는 요일 날짜 출력하기 (NEXT_DAY)

```sql
SELECT '2022/09/23' as 날짜, NEXT_DAY('2022-09-23', '목요일')
    FROM DUAL;
    
SELECT SYSDATE as "오늘 날짜"
    FROM DUAL;
    
SELECT NEXT_DAY(ADD_MONTHS('2022-09-23', 100), '목요일')
    FROM DUAL;    
```



### 029 특정 날짜가 있는 달의 마지막 날짜 출력하기 (LAST_DAY)

```sql
SELECT '2022/12/23' as 날짜, LAST_DAY('2022/12/23') as "마지막 날짜"
    FROM DUAL;
# 이달 말일까지 남은 일수
SELECT LAST_DAY(SYSDATE) - SYSDATE as "남은 날짜"
    FROM DUAL;
    
SELECT ename, hiredate, LAST_DAY(hiredate)
    FROM emp
    WHERE ename = 'KING';
```



### 030 문자형으로 데이터 유형 변환하기 (TO_CHAR, EXTRACT)

```sql
SELECT ename, TO_CHAR(hiredate, 'DAY') as 요일, TO_CHAR(sal, '999,999') as 월급
    FROM emp
    WHERE ename = 'SCOTT';
    
SELECT ename, TO_CHAR(hiredate, 'YYYY') as 연도, TO_CHAR(hiredate, 'MM') as 달,
    TO_CHAR(hiredate, 'DD') as 일, TO_CHAR(hiredate, 'DAY') as 요일
    FROM emp
    WHERE ename = 'SCOTT';

# 81년도에 입사한 사원의 이름과 입사일 출력    
SELECT ename, hiredate
    FROM emp
    WHERE TO_CHAR(hiredate, 'YYYY') = '1981';

SELECT ename as 이름, EXTRACT(year from hiredate) as 연도,
                        EXTRACT(month from hiredate) as 달,
                        EXTRACT(day from hiredate) as 요일
    FROM emp;
    
# 앞에 원화단위 표시
SELECT ename as 이름, TO_CHAR(sal*8700, 'L999,999,999') as 월급
    FROM emp;
```



### 031 날짜형으로 데이터 유형 변환하기 (TO_DATE)

```sql
SELECT ename, hiredate
    FROM emp
    WHERE hiredate = TO_DATE('81/11/17', 'RR/MM/DD');
```

- 이렇게 하면 날짜 형식과 관련 없이 검색 가능!!



### 032 암시적 형 변환 이해하기

```sql
SELECT ename, sal
    FROM emp
    WHERE sal = '3000';	# 오라클이 알아서 문자형을 숫자형으로 변환해 비교함
```



### 033 NULL 값 대신 다른 데이터 출력하기 (NVL, NVL2)

```sql
SELECT ename, comm, NVL(comm, 0)	# 기준값 / 기준값이 NULL일 때
    FROM emp;
    
SELECT ename, sal, comm, sal+comm
    FROM emp
    WHERE job IN ('SALESMAN', 'ANALYST');
    
SELECT ename, sal, comm, NVL(comm, 0), sal+NVL(comm, 0)
    FROM emp
    WHERE job IN ('SALESMAN', 'ANALYST');

SELECT ename, sal, comm, NVL2(comm, sal+comm, sal)	# 기준값 / 기준값이 NULL이 아닐 때 / 기준값이 NULL일 때
    FROM emp
    WHERE job IN ('SALESMAN', 'ANALYST');
```



### 034 IF문을 SQL로 구현하기 1 (DECODE)

```sql
SELECT ename, deptno, DECODE(deptno, 10, 300, 20, 400, 0) as 보너스	# default값(else) -> 0
    FROM emp;
    
SELECT empno, mod(empno, 2), DECODE(mod(empno, 2), 0, '짝수', 1, '홀수') as 보너스	# default 없음
    FROM emp;
    
SELECT ename, job, DECODE(job, 'SALESMAN', 5000, 2000) as 보너스	# if 다음에 바로 else
    FROM emp;
```



### 035 IF문을 SQL로 구현하기 2 (CASE)

```sql
SELECT ename, job, sal, CASE WHEN sal >= 3000 THEN 500
                            WHEN sal >= 2000 THEN 300
                            WHEN sal >= 1000 THEN 200
                            ELSE 0 END AS BONUS
    FROM emp
    WHERE job IN ('SALESMAN', 'ANALYST');
    
SELECT ename, job, comm, CASE WHEN comm IS NULL THEN 500
                                ELSE 0 END AS BONUS
    FROM emp
    WHERE job IN ('SALESMAN', 'ANALYST');
    
SELECT ename, job, CASE WHEN job IN ('SALESMAN', 'ANALYST') THEN 500
                    WHEN job IN ('CLERK', 'MANAGER') THEN 400
                    ELSE 0 END AS 보너스
    FROM emp;
```



### 036 최대값 출력하기(MAX)

```sql
SELECT MAX(sal)
    FROM emp;
    
SELECT MAX(sal)
    FROM emp
    WHERE job = 'SALESMAN';
    
SELECT job, MAX(sal)
    FROM emp
    WHERE job = 'SALESMAN'
    GROUP BY job;	# select로 job 컬럼의 여러 행이 출력되려고 하는데 Max값은 하나 > 데이터 그룹핑 필요
    
SELECT deptno, MAX(sal)
    FROM emp
    GROUP BY deptno;
```



### 037 최소값 출력하기 (MIN)

```sql
SELECT MIN(sal)
    FROM emp
    WHERE job = 'SALESMAN';
    
SELECT job, MIN(sal) 최소월급
    FROM emp
    GROUP BY job
    ORDER BY 최소월급 DESC;	# ORDER BY는 항상 마지막에!
    
SELECT MIN(sal)
    FROM emp
    WHERE 1 = 2;

SELECT job, MIN(sal) 최소월급
    FROM emp
    WHERE job != 'SALESMAN'
    GROUP BY job
    ORDER BY 최소월급 DESC;
```



### 038 평균값 출력하기 (AVG)

```sql
SELECT AVG(comm)	# 결과가 같아도 NULL을 제외하고 계산하므로 성능이 더 좋음(빠름).
	FROM emp;
	
SELECT AVG(NVL(comm, 0))	# NULL을 0으로 치환해서 결과값에 포함시킴
	FROM emp;
```



### 039 토탈값 출력하기 (SUM)

```sql
SELECT deptno, SUM(sal)
    FROM emp
    GROUP by deptno;
    
SELECT job, SUM(sal)
    FROM emp
    GROUP BY job
    ORDER BY SUM(sal) DESC;
    
SELECT job, SUM(sal)
    FROM emp
    WHERE SUM(sal) >= 4000
    GROUP BY job;
    
SELECT job, SUM(sal)
    FROM emp
    GROUP BY job
    HAVING SUM(sal) >= 4000;
    
SELECT job, SUM(sal)
    FROM emp
    WHERE job != 'SALESMAN'
    GROUP BY job
    HAVING SUM(sal) >= 4000;

SELECT job as 직업, SUM(sal)
    FROM emp
    WHERE job != 'SALESMAN'
    GROUP BY 직업
    HAVING SUM(sal) >= 4000;
```



### 040 건수 출력하기 (COUNT)

```sql
SELECT COUNT(empno)
    FROM emp;

SELECT COUNT(comm)
    FROM emp;
```

- COUNT는 NULL을 제외하고 센다. 평균값 등을 계산할 때 유의하자.
