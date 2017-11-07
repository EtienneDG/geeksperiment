---
id: 605
title: From Oracle to SQL Server
date: 2016-08-18T11:28:18+00:00
author: Etienne
layout: post
guid: http://geeksperiment.com/?p=605
permalink: /from-oracle-to-sql-server/
categories:
  - Tips and tricks
tags:
  - Oracle
  - SQL
  - SQL Server
---
I went from full Oracle to SQL Server and it is SO different&#8230;ok not that much. 🙂 But still I needed to found out how to do stuff with TSQL.
  
I guess there is ton of similar posts out there refrencing the differences between various DBMS qnd their languages. Nonetheless here is a few correpsondances, with use cases, between Oracle and SQL Server

  * **Compute an operation through a simple select**

  * PL/SQL:
  ```sql 
  SELECT 1+1 FROM dual;
  ```
  * TSQL :
  ```sql 
  SELECT 1+1
  ``` 

  * **Replace a null value**

<u>_NB:_</u>

To do an equivalent to Oracle&rsquo;s NVL2 a CASE statement is necessary (putting mutliple values in COALESCE will only select the first non _null_).

  * PL/SQL:
```sql 
SELECT NVL(LastName,'Doe') FROM Employee;
SELECT NVL2(workedHours, workedHours*hourlyRate, 0) FROM Work;
```
  * TSQL :
```sql 
SELECT COALESCE(FirstName,MiddleName,'N/A') FROM Employee
```

  * **Select only N lines**

  * PL/SQL:
```sql
SELECT * FROM Employee WHERE rownum <= 100
```

  * TSQL :
```sql 
SELECT TOP(100) * FROM Employee
```

  * **Select parent/children hierarchy from a table**

  * PL/SQL (cf. [Hierarchical queries](http://geeksperiment.com/2013/10/oraclelearn-hierarchical-queries-with-harry-potter/)): use of CONNECT BY PRIOR
  * TSQL : use Common Table Expression (aka CTE) via the WITH key word:
```sql 
;WITH CTE_Hierarchy AS (
  SELECT 0 AS depth
    ,e.EmployeeId
    ,e.Name
    ,e.ManagerId
  FROM test.Employee e
  UNION ALL 
  SELECT h.depth + 1 AS depth
    ,e2.EmployeeId
    ,e2.Name
    ,e2.ManagerId
  FROM test.Employee e2
  INNER JOIN CTE_Hierarchy h ON h.EmployeeId = e2.ManagerId
)
SELECT * FROM CTE_Hierarchy
```

There are still a few differences such as cursor browsing, no &laquo;&nbsp;FOR &#8230; IN &#8230;&nbsp;&raquo; syntax equivalent for SQL Server =(

Etc.  🙂