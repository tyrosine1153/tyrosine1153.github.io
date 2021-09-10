---
title: "[Programmers] SQL 고득점 Kit - SUM,MAX,MIN"
excerpt: "SQL 고득점 Kit - SUM,MAX,MIN (MySQL/Oracle)"

categories:
  - SQL
tags: 
  - [SQL, Programmers]

toc: true
toc_sticky: true

date: 2021-08-05
last_modified_at: 2021-08-05
---



## 최댓값 구하기

**MySQL**

```sql
SELECT DATETIME 
  FROM ANIMAL_INS
 ORDER BY DATETIME DESC
 LIMIT 1
```

 **Oracle**

```sql
SELECT DATETIME FROM
(
	SELECT DATETIME FROM ANIMAL_INS
	 ORDER BY DATETIME DESC
)
 WHERE ROWNUM <= 1
```



## 최솟값 구하기

**MySQL / Oracle**

```sql
SELECT MIN(DATETIME) 
  FROM ANIMAL_INS
```



## 동물 수 구하기

**MySQL / Oracle**

```sql
SELECT COUNT(*) 
  FROM ANIMAL_INS
```



## 중복 제거하기

**MySQL / Oracle**

```sql
SELECT COUNT(DISTINCT NAME) 
  FROM ANIMAL_INS
```

