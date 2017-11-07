---
id: 324
title: '[Oracle] Pseudo-Columns'
date: 2013-12-19T14:02:40+00:00
author: Etienne
layout: post
guid: http://geeksperiment.com/?p=324
permalink: /oracle-pseudo-columns/
image: /wp-content/uploads/2013/12/oracle_iron_man2.png
categories:
  - Tips and tricks
tags:
  - Oracle
  - SQL
  - tips
---
The features provided by Oracle to DBA and devs are simply awesome,legion and sometimes pretty much unknown by the masses.<!--more-->

Okay so pseudo-columns are &laquo;&nbsp;variables&nbsp;&raquo;, which values are assigned by Oracle. They are not stored on the disk but are accessible and queryable exactly like regular columns. I have already introduced one of them in a previous post (aka LEVEL in <a title="[Oracle]Learn hierarchical queries with Harry Potter" href="http://geeksperiment.com/oraclelearn-hierarchical-queries-with-harry-potter/" target="_blank">Learn hierarchical queries</a> ).

# The Usual Suspects

**<span style="font-size: medium; background-color: #3366ff; color: #ffffff;">sysdate</span>**

Get the current date and time of the base. The format depends on your NLS\_DATE\_LANGUAGE

```sql
  select sysdate from dual;
```

&nbsp;

<span style="font-size: medium; color: #ffffff; background-color: #3366ff;"><strong>rownum</strong></span>

Number of each line resutling from your query. Depends on your query and not on the record that is retrieved. The example below will only return one row.

```sql
  select emp_id from emp_tab where rownum =1;
```

&nbsp;

<span style="color: #ffffff; background-color: #3366ff; font-size: medium;"><strong>rowid</strong></span>

Number of each record,  unlike rownum this one is bound to each record. Again: only one row but this time the id is linked to the row.

<span style="font-family: Consolas, Monaco, monospace; font-size: 12px; line-height: 18px;">

```sql
  select emp_id from emp_tab where rowid =1;
```

&nbsp;

**<span style="font-size: medium; color: #ffffff; background-color: #3366ff;">level</span>** Used with the _connect by_ clause. Hierarchical queries oragnize stuff in trees and this pseudo-column shows which level is each row on. (starts at 0)

```sql
  select ancestor,level from weasleys
  connect by prior ancestor = child
  starts with = 'XXX';
```
  
&nbsp;

<span style="font-size: medium; color: #ffffff; background-color: #3366ff;"><strong>curval / nextval </strong></span>

These two are used for sequences and are quite self explainatory  😉

```sql
  select my_sequence.curval from dual;
```

&nbsp;

<span style="font-size: medium; color: #ffffff; background-color: #3366ff;"><strong style="line-height: 1.5em;">ora_scn</strong></span>

SCN for System Change Number, this number tracks data modifications. It&rsquo;s really helpful when you want to know which records where last edited.

```sql
  select * from emp_table 
  order by ora_rowscn desc;
```

&nbsp;

# More examples

### <span style="font-size: 1.17em; line-height: 1.5em;">How to split a string in Oracle</span>

To split a row we are going to use the level pseudo-column and its connect by clause&#8230;and a little regular expression.  😆

The level pseudo-column is used to &laquo;&nbsp;generate&nbsp;&raquo; rows. For example this query creates four rows

```sql
  select level from dual connect by level = 4;

 LEVEL

----------

 1

 2

 3

 4
 ```

 Then the regexp_substr works just like a regular susbstr : you can specify a starting position and the number of the occurence you want.

```sql
  substr : string to search , position to start, length
  regexp_substr: string to search, pattern, position to start, nth occurence</code></pre>
```

Everything put together looks something like this:

```sql
  select level as lv, regexp_substr ('smith,jones,doe,joe', '[^,]+', 1, level) name
  FROM dual
  CONNECT BY regexp_substr('smith,jones,doe,joe', '[^,]+', 1, level) is not null;

 LV    NAME

---    ------- 

 1     smith

 2     jones

 3     doe

 4     joe
  
```

&nbsp;

### How to know the last updated time of a row

Ora_rowscn will do the job, however this is what you get when using it raw:

```sql
  select emp_no,ora_rowscn from employee
   where rownum = 1
   order by ora_rowscn desc;

EMP_NO      ORA_ROWSCN

----------- ----------

 ALTO       2536313481
```

No worries though, you can easily convert it to a readable date with the scn\_to\_timestamp. But careful the retention time of scn number is not infinite so might get a ORA-08181 error. Anyways:

```sql
  select emp_no, scn_to_timestamp(ora_rowscn) as timestamp
  from employee_tab et
  where emp_no = '1234A'
  order by ora_rowscn desc;

EMP_NO      TIMESTAMP

----------- -------------------------------------------------

1234A       19/12/13 13:49:21,000000000
```