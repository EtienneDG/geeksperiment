---
id: 696
title: Angular 2 why is it better ? (webinar sum up)
date: 2016-12-16T18:01:52+00:00
author: Etienne
layout: post
guid: http://geeksperiment.com/?p=696
permalink: /angular-2-why-is-it-better-webinar-sum-up/
image: /wp-content/uploads/2016/12/images.jpg
categories:
  - Random
tags:
  - angular
  - javascript
  - tutorial
  - webinar
---
This article is a recap of the webinar of [scotch.io](http://scotch.io/)
  
<https://scotch.io/bar-talk/webinar-10-ways-angular-2-makes-development-easier>

* * *

## <a id="Introduction_7"></a>Introduction

> Angular 2.0 crazy difficult ?
  
> ES6 ? Transpiling JS code ? Build JS application ?..

### <a id="ES6_11"></a>ES6

ES5 is the “current version of JS”. ES5 is a subset of ES6/7 so its ES6 is fully retro-compatible.
  
**However** due to limited support of the actual internet browsers we need to transpile “down” to ES5.
  
Transpiling can be done with Babel and also using Typescript.

New stuff in ES6:

  * Arrow functions
  * Classes
  * Module loaders (using Webpack for example)
  * Template strings (~ string interpolation)

### <a id="Typescript_23"></a>Typescript

Why would I use it? It transpiles to ES5 for you AND help you catch errors early in development process.
  
You don’t have to wait for runtime to get the JS error.

* * *

## <a id="Angular_1_vs_2_performance_wise_28"></a>Angular 1 vs 2 (performance wise)

> 5x time fastest rendering

Thanks to a simpler flow between the components.
  
**Angular 1** had mutliple (ie. data and event)
  
flows going down to child components but also up to parent components.
  
**Angular 2** only has data going down to child components and events going up to parent components.

> 2.5x time fastest load time

**Angular 1** JiT ⇒ Parse + Generate and then Render
  
**Angular 2** AoT ⇒ Render

AoT (ahead of time) vs JiT (just in time) : the Angular compiler is Ahead of Time which means
  
the application is sent already compiled to the browser. This also positively affects application size.

> 3x smaller app size (with AoT)

* * *

## <a id="Templating_49"></a>Templating

**Interpolation** : with doubled curly brackets

```html
<span>{{message}}</span>
```

_(NEW)_ **Property binding** : with square brackets

```html
<img [src]="user.image" alt="myImage" /> 
```

**Event mapping** : with parenthesis

```html
<img (click)="doStuff()" alt="myImage" />  <!--Formerly ng-click--> 
```

**Strucural directive** (eg. loops, conditions, …) : with star
```html
<div *ngFor="let user of users">
    <span>{{user.name}}</span>
</div> 
```

## <a id="Components_69"></a>Components

Split the app into “discrete functional chunks”. Discrete means that components
  
can work as separate parts. The components should have their own responsability and handle their
  
own concerns.
  
A component is generaly composed of:

  * a selector (that can be use in other components templates)
  * a template

Example in Typescript:

```javascript
@Component({
    selector : "my-component",
    template : `<div id="{{user.id}}} (click)="sayName(user)">{{user.name}}</div>`
})

export class Mycomponent implements OnInit{
    user : any
    ngOnInit(){
        user = {id : 1, name : "John"}
    }

    sayName(user){
        alert(user.name);
    }
}
```

**Tip** : easy component initialisation with [angular-cli](https://cli.angular.io/)

Starting from **Angular 2** components are isolated, for example defining the same css class
  
in two different components won’t create “styling conflicts”. This is thanks to custom attributes
  
added bu Angular at rendering time.

* * *

## <a id="In_the_end__Why_it_makes_dev_easier__103"></a>In the end : Why it makes dev easier ?

ES6 + Typescript : errors detected sooner, limits the pain of JS
  
Consistency of concepts in the framework, makes behaviour predictable and helps to build stuff quickly.
  
Multiplatform
  
Style encapsulation
  
The learning curve is there though, but the docs are better than V1.x.
  
Tooling (IDE, browser extension, templating packages) is now also better and more stable.