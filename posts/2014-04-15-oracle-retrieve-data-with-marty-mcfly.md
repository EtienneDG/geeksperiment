---
id: 361
title: '[Oracle] Retrieve data with Marty McFly'
date: 2014-04-15T21:36:57+00:00
author: Etienne
excerpt: |
  You misclicked and deleted a "few" rows that you shouldn't have? You compiled a package, overwriting the previous code, but you didn't want that? Don't worry Oracle has its own built-in DeLorean : flashback queries.
layout: post
guid: http://geeksperiment.com/?p=361
permalink: /oracle-retrieve-data-with-marty-mcfly/
image: /wp-content/uploads/2014/04/flashback_query_commic_strip2.png
categories:
  - Tips and tricks
tags:
  - flashback
  - Oracle
  - SQL
  - tips
---
You misclicked and deleted a &laquo;&nbsp;few&nbsp;&raquo; rows that you shouldn&rsquo;t have? You compiled a package, overwriting the previous code, but you didn&rsquo;t want that? Don&rsquo;t worry Oracle has its own built-in DeLorean : flashback queries.Flashback queries, is a feature that allows you to do query past data, i.e. as it was at a certain given date. Needless to say it&rsquo;s quite powerful and really usefull.

&nbsp;

<p style="text-align: center;">
  <a href="http://geeksperiment.com/wp-content/uploads/2014/04/flashback_query_commic_strip2.png"><img class=" wp-image-473  aligncenter" src="http://geeksperiment.com/wp-content/uploads/2014/04/flashback_query_commic_strip2-1024x364.png" alt="flashback_query_comic_strip" width="900" height="319" srcset="http://geeksperiment.com/wp-content/uploads/2014/04/flashback_query_commic_strip2-1024x364.png 1024w, http://geeksperiment.com/wp-content/uploads/2014/04/flashback_query_commic_strip2.png 1093w" sizes="(max-width: 900px) 100vw, 900px" /></a>
</p>

## How to use it

The usage is quite simple, you only need to specify the date for which you want to query your data, using the keys words &laquo;&nbsp;AS OF&nbsp;&raquo;:

```sql
SQL> create table mytab (id number, value varchar(100));
Table created

SQL> insert into mytab (id,value) values (1,'old_value_1');
1 row inserted

SQL> insert into mytab (id,value) values (2,'old_value_2');
1 row inserted

SQL> insert into mytab (id,value) values (3,'old_value_3');
1 row inserted

SQL> select to_char(sysdate,'DD/MM/YYYY HH24:MI:SS') DT from dual;
DT

-------------------

15/04/2014 17:39:47

SQL> update mytab set value = 'new_value_1' where id = 1;

1 row updated

SQL> select to_char(sysdate,'DD/MM/YYYY HH24:MI:SS') DT from dual;

DT

-------------------

15/04/2014 17:40:02

SQL> select * from mytab;

        ID VALUE

---------- --------------------------------------------------------------------------------

         1 new_value_1

         2 old_value_2

         3 old_value_3

SQL> select * from mytab as of timestamp to_timestamp('15/04/2014 17:39:47','DD/MM/YYYY HH24:MI:SS');

        ID VALUE

---------- --------------------------------------------------------------------------------

         1 old_value_1

         2 old_value_2

         3 old_value_3
         
```

Pretty simple. You can only do flashbacks query with TIMESTAMPS and SCN ([see previous post for SCN infos](http://geeksperiment.com/oracle-pseudo-columns/ "[Oracle] Pseudo-Columns")) in the where clause.

Since its a simple select query you can do for examples:

  * Create a backup of your data as it was yesterday:
  ```sql
  Create mytab_bak as select * from my_tab as of timestamp trunc(sysdate) -1;
  ```

  * Retrieve an older version of a package: (reserved to SYSDBA profiles, sadly)
  
  ```sql
select * from sys.user_source as of timestamp sysdate-1
where name = 'MY_PACKAGE'
and type = 'PACKAGE BODY';
```

## Limitations

To use flashback queries you need to (as always) :

  * Have sufficient rights,

```sql
grant FLASHBACK on mytab to myuser;
```

  * A compatible Oracle version, that would be 10g+ ,
  * Flashback queries are enabled on your DB, this configuration is to be done with DBA rights ([undo management](http://docs.oracle.com/cd/B12037_01/server.101/b10739/undo.htm#ADMIN013)).

Also note that since flashback queries are using the undo tablespace, you can only do queries on data in a given period of time depending on UNDO_RETENTION parameter. Retention time is _usually_ a week or so.

&nbsp;