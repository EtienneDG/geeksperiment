---
Title: Javascript Rage Quit Cycle, aka my own JS Fatigue
permalink: javascript-rage-quit-cycle-aka-my-own-js-Fatigue
date: 2017-09-12T18:01:52+00:00
tags:
  - JS
  - react
  - fatigue
---

## A bit of context
I''m a fullstack developper...so yes : I write DB, back end and front end code with Microsoft stack, so mostly C# ASP.NET MVC and SQL Server for the back end (and part of the front because I like Razor :) ).

A few years ago (2012) I played around with Angular (1.X), then abandonned it due to the lack of interest from other devs and lead tech in my workplace back then. I was was on the "JS is Evil" side and using Angular had an unexpected effect : I finally found JS quite appealing, not perfect still a bit obscure, but much more powerful and interesting that I would have thought.

## The future is here !

Ok fast forward. I worked with a bunch (a lot actually) of other devs of the "JS is Evil" side, but whatever but we now have TONS of cool language features (ES6 !), neat frameworks and quite a lot of documentation. So the "Meh JS is pretty neat actually" side is growing in number. At least it seems so from the popularity of new frameworks, concepts, tools all written in JS. It took time to get to this degree of "acceptance" from the dev world.

Today for some unknown reason (to me at least), people actively working in the JS OSS community now seem very impatient and are moving forward at an insane pace. Stuff created from packages and code blocks from a month ago is quite likely to be outdated or obsolete today. The JS ecossystem has never been this rich and at the same time never so messy. Here starts the infamous "JS fatigue" or for me the **"JS RQC" : Javascript Rage Quit Cycle**.

## Why we can't have nice things

The "JS fatigue" term seems a bit outdated now in 2017 and I experienced it bit differently. I know JS, ES6, I'm familiar with authentication, dependency injection,bundling, routing, minifying, testing, mocking, ... Basically all the stuff that I use as a web developer. However, I must admit that, as many devs (?), I'm lazy and reimplementing authentication, routing,etc. by code in my app instead of having a pre-configured framework doing it for me is PAINFUL.

THIS IS NOT THE CODE I WANT TO WRITE. 

I want to write code to build an app for my users, I want to bring them value through features. All the things I just mentionned are boilerplate code and should be hidden away (still available to compulse) to decrease the noise when I'm coding.

But I'm a developer and I like to understand how things work in order to build something with it. So I go head first, trying to poke around the config, readings the docs, trying to plug things together, trying various packages, trying to add a component library to a template,etc. The key word here is *"trying"*.

In the end, it's taking me way more time that I'd want to spend on this. So after a while I tend to give up...and come back to it a few days later. Here you have it : **the Rage Quit Cycle**. Various issues can trigger it, sometimes its the configuration (wepback, yarn,...), sometimes its the packages dependencies, sometimes its obsolete package from two months ago, you get the idea.


## Lets take an example

Recently I wanted to create a simple app with only JS. So I decided to go with the **MERN stack**. MERN (mern.io) actually provides a template "mern-starter", lets clone and npm install.  
![1700 packages](/img/2017/mern_npm_install.PNG "1700 packages")


OK...for a starter template it seems quite "complete", it has: 
- docker files
- blueprints
- redux
- parallelized tests
- module pattern for the client component
- ...

Ok that's a bit to much for me right now, I'd just like a simpler "Hello World" so I kept reading the docs and found there is a template generator. Lets try it out:  
![Only one template](/img/2017/mern_list.PNG "Only one template")

Oh...there is only the starter template and that's understandable, it's an OSS project maintained by a community so I d'ont expect dozens of template. Lets take the mern-starter, but I want to have my own client side lets say a simpler one whith only a few components, no Redux, no module pattern, just plain jsx components. A few hours later, I hit once again the end of a cycle : I gave up.

I'll get back to it in a few days...I guess.