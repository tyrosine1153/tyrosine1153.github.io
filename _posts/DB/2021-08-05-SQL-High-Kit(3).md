---
title: "[Programmers] SQL 고득점 Kit - GROUP BY"
excerpt: "SQL 고득점 Kit - GROUP BY (MySQL/Oracle)"

categories:
  - SQL
tags: 
  - [SQL, Programmers]

toc: true
toc_sticky: true

date: 2021-08-05
last_modified_at: 2021-08-05
---



## 고양이와 개는 몇 마리 있을까

**MySQL / Oracle**

```sql
SELECT ANIMAL_TYPE, COUNT(ANIMAL_TYPE) 
  FROM ANIMAL_INS
 GROUP BY ANIMAL_TYPE
 ORDER BY ANIMAL_TYPE ASC
```



## 동명 동물 수 찾기

**MySQL / Oracle**

```sql
SELECT NAME, COUNT(NAME) NM_COUNT 
  FROM ANIMAL_INS
 GROUP BY NAME
HAVING COUNT(NAME) > 1
 ORDER BY NAME ASC
```



## 입양 시각 구하기(1)

**MySQL**

```sql
SELECT HOUR(DATETIME), COUNT(*) 
  FROM ANIMAL_OUTS
 WHERE HOUR(DATETIME) BETWEEN 9 AND 19
 GROUP BY HOUR(DATETIME)
 ORDER BY HOUR(DATETIME) ASC
```



**Oracle**

```sql
SELECT EXTRACT(HOUR FROM CAST(DATETIME AS TIMESTAMP)), COUNT(*) 
  FROM ANIMAL_OUTS
 WHERE EXTRACT(HOUR FROM CAST(DATETIME AS TIMESTAMP)) BETWEEN 9 AND 19
 GROUP BY EXTRACT(HOUR FROM CAST(DATETIME AS TIMESTAMP))
 ORDER BY EXTRACT(HOUR FROM CAST(DATETIME AS TIMESTAMP)) ASC
```



## 입양 시각 구하기(2)



