## 상위 n개 레코드

```sql
SELECT NAME FROM ANIMAL_INS ORDER BY DATETIME FETCH NEXT 1 ROW ONLY;
```

> 2개 이상의 레코드 출력할 때는 ROW -> ROWS