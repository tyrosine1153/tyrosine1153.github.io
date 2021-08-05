---
title: "[Programmers] SQL 고득점 Kit - IS NULL"
excerpt: "SQL 고득점 Kit - IS NULL (MySQL/Oracle)"

categories:
  - SQL
tags: 
  - [SQL, Programmers]

toc: true
toc_sticky: true

date: 2021-08-05
last_modified_at: 2021-08-05
---



## 이름이 없는 동물의 아이디

**MySQL / Oracle**

```sql
SELECT ANIMAL_ID 
  FROM ANIMAL_INS
 WHERE NAME IS NULL
 ORDER BY ANIMAL_ID ASC
```



## 이름이 있는 동물의 아이디

**MySQL / Oracle**

```sql
SELECT ANIMAL_ID 
  FROM ANIMAL_INS
 WHERE NAME IS NOT NULL
 ORDER BY ANIMAL_ID ASC
```



## NULL 처리하기

**MySQL**

```sql
SELECT ANIMAL_TYPE, IFNULL(NAME, "No name"), SEX_UPON_INTAKE 
  FROM ANIMAL_INS
 ORDER BY ANIMAL_ID ASC
```

**Oracle**

```sql
SELECT ANIMAL_TYPE, NVL(NAME, 'No name'), SEX_UPON_INTAKE 
  FROM ANIMAL_INS
 ORDER BY ANIMAL_ID ASC
```



