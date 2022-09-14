## 초보자를 위한 SQL 200제



### 001 특정 열 선택하기

```sql
SELECT empno, ename, sal
	FROM emp;
```

> 가독성을 위해 SQL은 대문자, 컬럼명과 테이블명은 소문자로 작성
>
> 한 줄에 써도 되지만 SQL문별로 줄바꿈 및 들여쓰기 권장 > 길어지는 SQL 대비



### 002 모든 열 출력하기

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

