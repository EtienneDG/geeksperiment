---
id: 687
title: First glimpse at Promises in JS
date: 2016-08-26T09:50:50+00:00
author: Etienne
layout: post
guid: http://geeksperiment.com/?p=687
permalink: /first-glimpse-at-promises-in-js/
categories:
  - Uncategorized
tags:
  - javascript
  - promise
---
Promise is a programmation pattern
  
Promise is a new object in Javascript. It is specified in ES6 (2015)
  
The main goal of this new spec is to implement a better way to do async calls/behaviours.

&nbsp;

# Continuous passing and direct styles

&laquo;&nbsp;Today&nbsp;&raquo; we mostly do asynchronous behaviour in JS through callbacks. It force Continuous-passing style (CPS)
  
Usually we rather &laquo;&nbsp;use&nbsp;&raquo; direct style over continuous passing style. We use continuous passing style when we use $.ajax for example (and more widely when we do basic async in JS)

To explain the difference between CPS and DS lets take a classical example.

&nbsp;

### Direct style factorial:

```javascript
function fact(n) {
  if (n == 0)
    return 1 ;
  else
    return n * fact(n-1) ;
}
//we call it like this
console.log(fact(10));
```

### Continuation-passing style factorial:

```javascript
function fact(n,ret) {
  if (n == 0)
    ret(1) ;
  else
    fact(n-1, function (t0) {
    ret(n * t0) }) ;
}
//we call it like this
fact(10,function(result){
console.log(result)
})
```

# Promises

A promise is the &laquo;&nbsp;eventual result&nbsp;&raquo; of an async operation. This abstraction are used as &laquo;&nbsp;proxies&nbsp;&raquo; (read placeholder object) whose values/result are not yet known. They will be known &laquo;&nbsp;later&nbsp;&raquo; during execution.
  
So similar example as before, but for some random async behaviour:

### Before we had :

```javascript
getStuffAync("myParam",function(err,result){
//This is the body of my callback
})
```

### Now we have :

```javascript
  var promiseOfStuff = getStuffAync("myParam");
```

# Yes but why in JS?

In javascript you only have one thread, yes just one. So two blocs of a script cannot run simultaneously. Moreover (depending on the browser) this thread is shared to other stuff, such as the rendering engine (painting), user&rsquo;s inputs,etc.
  
So this shared resource can quickly become a bottleneck for a page loading/rendering.

So we usually use events, listeners and triggers. The downside of it is of we have multiples events on one object or that we want to handle on failure or know when all events are done, etc.

The difference is that we can handle promise (so &laquo;&nbsp;async object&nbsp;&raquo; or at least an abstraction of them) as real objects.

### Examples:

```javascript
function getStuff(url) {

return new Promise(function(resolve, reject) {
  //We create a new Request object...
  var request = new XMLHttpRequest();
  //...and init its properties
  request.open(&#039;GET&#039;, url);
  request.onload = function() {
    
    if (request.status === 200) {
      resolve(request.response);
    } 
    else {
      reject(Error(&#039;Something went south&#039;));
    }
    };

    request.onerror = function() {
      reject(Error(&#039;There was a network error.&#039;));
    };

      //Exec the request
      request.send();//this is synchronous
    });
  }

getStuff("/testUrl").then(function(response) { console.log("We got our content") }, function(Error) {alert(Error);});
//Second syntax
getStuff("/testUrl").then(function(response) { console.log("We got our content") }).catch(function(Error) {alert(Error);});
```


  
The then function has two params. First one is executed when the promise is successful and the second one when the promise fails.
  
Is my example the thing to understand is that most params are functions so here :
  
resolve => console.log(/\*some text\*/) reject => alert(/\*some text\*/)</p> 

&nbsp;

# Other cool things about promises

You can define a sequence of promise, with a specific order in for their &laquo;&nbsp;then&nbsp;&raquo;

<span style="text-decoration: underline;">SOURCES:</span>

<https://en.wikipedia.org/wiki/Continuation-passing_style>
  
<https://blog.domenic.me/youre-missing-the-point-of-promises/>
  
<http://www.slideshare.net/domenicdenicola/callbacks-promises-and-coroutines-oh-my-the-evolution-of-asynchronicity-in-javascript>
  
<http://www.html5rocks.com/en/tutorials/es6/promises/#toc-async>