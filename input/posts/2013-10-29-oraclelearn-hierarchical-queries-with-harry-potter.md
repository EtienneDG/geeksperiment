---
id: 293
title: '[Oracle]Learn hierarchical queries with Harry Potter'
date: 2013-10-29T18:31:22+00:00
author: Etienne
layout: post
guid: http://geeksperiment.com/?p=293
permalink: /oraclelearn-hierarchical-queries-with-harry-potter/
image: /wp-content/uploads/2013/10/ron-weasley-thumb2.png
categories:
  - Tips and tricks
tags:
  - connect
  - hierarchical
  - Oracle
  - prior
  - SQL
  - tips
---
# Trees, trees everywhere!

A hierarchy is a structure of items organized by levels, the global structure is often call a tree. It is one of the most common way of organizing objects and it can be a lot of things :

  * <a href="http://upload.wikimedia.org/wikipedia/commons/2/2e/Organizational_chart.svg" target="_blank">Company organisation</a>
  * <a href="http://upload.wikimedia.org/wikipedia/commons/a/a5/Biological_classification_L_Pengo_vflip.svg" target="_blank">Animal species</a>
  * Your computer file system

<!--more-->

  * &#8230;.

Typically an hierarchical query would be:

_&laquo;&nbsp;Who are the employees that John is in charge of?&nbsp;&raquo;_

&laquo;&nbsp;Which are the species under the Hominidae family?&nbsp;&raquo;

&laquo;&nbsp;How many folders does my hard drive contain?&nbsp;&raquo;

# Meet the Weasleys

So the visual representation of a hierarchy/tree is easy, however the data modeling is a bit different. I&rsquo;ll take a simple case to illustrate this: a family tree. In a family every member is a person with two parents/ancestors (which are persons too): so one table to gather them all!

Here is an _small_ portion of the Weasley family:

[<img class="aligncenter wp-image-299 size-full" src="http://geeksperiment.com/wp-content/uploads/2013/10/weasley-tree2.png" alt="weasley-family-tree" width="545" height="206" srcset="http://geeksperiment.com/wp-content/uploads/2013/10/weasley-tree2.png 545w, http://geeksperiment.com/wp-content/uploads/2013/10/weasley-tree2-300x113.png 300w, http://geeksperiment.com/wp-content/uploads/2013/10/weasley-tree2-265x100.png 265w" sizes="(max-width: 545px) 100vw, 545px" />](http://geeksperiment.com/wp-content/uploads/2013/10/weasley-tree2.png)

&nbsp;

The tree has 3 levels (3 generations): Arthur and Molly are the roots and their grand children are the leafs. Let&rsquo;s get these people into a table:

```sql
CREATE TABLE weasleys 

(

     child varchar(30),

     ancestor varchar(30)

);

Insert Into weasleys (child,ancestor) values (&#039;Ron&#039;,&#039;Arthur&#039;);

Insert Into weasleys (child,ancestor) values (&#039;Ron&#039;,&#039;Molly&#039;);

Insert Into weasleys (child,ancestor) values (&#039;Ginny&#039;,&#039;Arthur&#039;);

Insert Into weasleys (child,ancestor) values (&#039;Ginny&#039;,&#039;Molly&#039;);

Insert Into weasleys (child,ancestor) values (&#039;Hugo&#039;,&#039;Ron&#039;);

Insert Into weasleys (child,ancestor) values (&#039;Hugo&#039;,&#039;Hermione&#039;);

Insert Into weasleys (child,ancestor) values (&#039;Rose&#039;,&#039;Ron&#039;);

Insert Into weasleys (child,ancestor) values (&#039;Rose&#039;,&#039;Hermione&#039;);

Insert Into weasleys (child,ancestor) values (&#039;James&#039;,&#039;Ginny&#039;);

Insert Into weasleys (child,ancestor) values (&#039;James&#039;,&#039;Harry&#039;);

Insert Into weasleys (child,ancestor) values (&#039;Albus&#039;,&#039;Ginny&#039;);

Insert Into weasleys (child,ancestor) values (&#039;Albus&#039;,&#039;Harry&#039;);

Insert Into weasleys (child,ancestor) values (&#039;Lily&#039;,&#039;Ginny&#039;);

Insert Into weasleys (child,ancestor) values (&#039;Lily&#039;,&#039;Harry&#039;);</code></pre>
 ```

# Who&rsquo;s your daddy?

And now Oracle voodoo&#8230;nah actually it&rsquo;s quite simple. By using &lsquo;CONNECT BY PRIOR&rsquo; and &lsquo;START WITH&rsquo; you can achieve almost everything. The only thing to get is which direction you want to go: to the roots? to the leafs?

### To the roots

For example: Name all the ancestors of &lsquo;Albus&rsquo;. The search will &lsquo;START WITH&rsquo; Albus and we want to get Albus ancestor who is also the child of Albus ancestor&rsquo;s ancestor, which translates in :

```sql
connect by prior ancestor = child

start with child = &#039;Albus&#039;;

ANCESTOR

------------------------------

Ginny

Arthur

Molly

Harry
```

Nice! Let&rsquo;s add a little something : the pseudo-column &lsquo;LEVEL&rsquo;, it&rsquo;s pretty self explainatory (start level is 0) and concatenate all that:
```sql
connect by prior ancestor = child

start with child = &#039;Albus&#039;

group by level;

LEVEL NAMES

----- ------------------------------------------------------------

    1 Ginny,Harry

    2 Arthur,Molly
```

### To the leafs

And the other way: Every child and grand child of Molly:

```sql
SQL> select child, ancestor, level from weasleys t

  2  connect by prior child = ancestor

  3  start with ancestor = &#039;Molly&#039;

  4  order by level asc,ancestor;

CHILD                          ANCESTOR                            LEVEL

------------------------------ ------------------------------ ----------

Ron                            Molly                                   1

Ginny                          Molly                                   1

James                          Ginny                                   2

Albus                          Ginny                                   2

Lily                           Ginny                                   2

Hugo                           Ron                                     2

Rose                           Ron                                     2
  
```