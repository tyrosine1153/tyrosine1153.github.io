---
title: "[Programmers] SQL 고득점 Kit - SELECT"
excerpt: "SQL 고득점 Kit - SELECT (MySQL/Oracle)"

categories:
  - SQL
tags: 
  - [SQL, Programmers]

toc: true
toc_sticky: true

date: 2021-06-29
last_modified_at: 2021-06-29
---



## 모든 레코드 조회하기

**MySQL / Oracle**

```sql
SELECT * 
  FROM ANIMAL_INS
 ORDER BY ANIMAL_ID ASC
```



## 역순 정렬하기

**MySQL / Oracle**

```sql
SELECT NAME, DATETIME 
  FROM ANIMAL_INS
 ORDER BY ANIMAL_ID DESC
```



## 아픈 동물 찾기

**MySQL / Oracle**

```sql
SELECT ANIMAL_ID, NAME
  FROM ANIMAL_INS
 WHERE INTAKE_CONDITION = 'Sick'
 ORDER BY ANIMAL_ID ASC
```



## 어린 동물 찾기

**MySQL / Oracle**

```sql
SELECT ANIMAL_ID, NAME 
  FROM ANIMAL_INS
 WHERE INTAKE_CONDITION != 'Aged'
 ORDER BY ANIMAL_ID ASC
```



## 동물의 아이디와 이름

**MySQL / Oracle**

```sql
SELECT ANIMAL_ID, NAME 
  FROM ANIMAL_INS
 ORDER BY ANIMAL_ID ASC
```



## 여러 기준으로 정렬하기

**MySQL / Oracle**

```sql
SELECT ANIMAL_ID, NAME, DATETIME 
  FROM ANIMAL_INS
 ORDER BY NAME ASC, DATETIME DESC
```



## 상위 n개 레코드

**MySQL**

```sql
SELECT NAME 
  FROM ANIMAL_INS
 ORDER BY DATETIME ASC
 LIMIT 1
```

**Oracle**

```sql
SELECT * 
FROM
(
    SELECT NAME 
      FROM ANIMAL_INS
     ORDER BY DATETIME ASC
)
WHERE ROWNUM <= 1
```

