---
title: "[Programmers] SQL 고득점 Kit - String,Date"
excerpt: "SQL 고득점 Kit - String,Date (MySQL/Oracle)"

categories:
  - SQL
tags: 
  - [SQL, Programmers]

toc: true
toc_sticky: true

date: 2021-08-05
last_modified_at: 2021-08-05
---



## 루시와 엘라 찾기

**MySQL / Oracle**

```sql
SELECT ANIMAL_ID, NAME, SEX_UPON_INTAKE
  FROM ANIMAL_INS
 WHERE NAME IN('Lucy', 'Ella', 'Pickle', 'Rogan', 'Sabrina', 'Mitty')
 ORDER BY ANIMAL_ID ASC
```



## 이름에 el이 들어가는 동물 찾기

**MySQL**

```sql
SELECT ANIMAL_ID, NAME 
  FROM ANIMAL_INS
 WHERE NAME LIKE "%EL%" AND ANIMAL_TYPE = 'Dog'
 ORDER BY NAME
```

**Oracle**

```sql
SELECT ANIMAL_ID, NAME 
  FROM ANIMAL_INS
 WHERE UPPER(NAME) LIKE '%EL%' 
   AND ANIMAL_TYPE = 'Dog'
 ORDER BY NAME
```



## 중성화 여부 파악하기

**MySQL / Oracle**

```sql
SELECT ANIMAL_ID, NAME,
  CASE WHEN SEX_UPON_INTAKE LIKE 'Intact%' THEN 'X'
       ELSE 'O' END
  FROM ANIMAL_INS
 ORDER BY ANIMAL_ID
```



## 오랜 기간 보호한 동물(2)

**MySQL**

```sql
SELECT I.ANIMAL_ID, I.NAME
  FROM ANIMAL_INS I JOIN ANIMAL_OUTS O
    ON I.ANIMAL_ID = O.ANIMAL_ID
 ORDER BY DATEDIFF(O.DATETIME, I.DATETIME) DESC
 LIMIT 2
```

**Oracle**

```sql
SELECT * 
FROM
(
    SELECT I.ANIMAL_ID, I.NAME
      FROM ANIMAL_INS I JOIN ANIMAL_OUTS O
        ON I.ANIMAL_ID = O.ANIMAL_ID
     ORDER BY O.DATETIME- I.DATETIME DESC
)
WHERE ROWNUM <= 2
```



## DATETIME에서 DATE로 형 변환

**MySQL**

```sql
SELECT ANIMAL_ID, NAME, date_format(DATETIME, '%Y-%m-%d') 
  FROM ANIMAL_INS
 ORDER BY ANIMAL_ID
```

**Oracle**

```sql
SELECT ANIMAL_ID, NAME, TO_CHAR(DATETIME, 'YYYY-MM-DD') 
  FROM ANIMAL_INS
 ORDER BY ANIMAL_ID
```

