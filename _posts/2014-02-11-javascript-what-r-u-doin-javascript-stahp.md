---
id: 425
title: Javascript what R U doin? Javascript, stahp!
date: 2014-02-11T09:45:06+00:00
author: Etienne
layout: post
guid: http://geeksperiment.com/?p=425
permalink: /javascript-what-r-u-doin-javascript-stahp/
image: /wp-content/uploads/2014/02/omg_wtf_cat.thumbnail2.jpg
categories:
  - Random
tags:
  - javascript
  - random
---
I like javascript&#8230;

<!--more-->

I know that can sound a bit weird, but I really do like JS. I mean it&rsquo;s got all these tools, frameworks, which allows you to achieve complex things in little time. Moreover it&rsquo;s very accessible and easy to learn.

I was playing around with a page&rsquo;s DOM when I noticed something weird, just see for yourself:

Lets say the htlm is (doesn&rsquo;t really matter):

```html
<div>
    <span></span>
</div>
```

And say I&rsquo;m using a jQuery selector that doesn&rsquo;t return anything such as _$("&nbsp;div td&nbsp;")_ for instance and then print out the length of the result. Here is what happen:

```javascript
var test;
var test1;
var test2;
test.length //Cannot read property &lsquo;length&rsquo; of undefined, quite normal
test1 =  $("div td"); //length : 0
test2 += $("div td");//length : 24!!! Because  test2 : undefined[object Object]
```

(<http://jsfiddle.net/kirb/7EJJq/>)

It seems like using a shorthand assignement operator on a an not yet assigned variable is not a good idea&#8230;still the outcome is quite unexpected.

For more JS "&nbsp;fun&nbsp;" : <http://wtfjs.com/>