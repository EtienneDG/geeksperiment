---
id: 172
title: What the BEEP? or how to desactivate UC sound
date: 2013-09-06T10:42:23+00:00
author: Etienne
layout: post
guid: http://geeksperiment.com/?p=172
permalink: /what-the-beep-or-how-to-desactivate-uc-sound/
image: /wp-content/uploads/2013/09/beep_beeep2.png
categories:
  - Tips and tricks
---
I work on a <del datetime="2013-09-06T08:21:27+00:00">old</del> historical (as we say here) software, that actually runs on Windows Server 2003. I have a laptop so I&rsquo;m not having any trouble with UC sound, however we have a few desktop computers that we used to connect to an integration environment. On those machines EACH TIME a pop up appears in the software the UC beeps.<!--more-->

It might not seem that annoying but almost every creation/edit in the software has a confirmation pop up&#8230;so you hear the beep&#8230;a lot! Adidng to that, I&rsquo;m sitting next to the machines from hell, so even when I don&rsquo;t use them and others do, I hear that f-\*beep\*-ing sound.

But fear not, because there is a really simple command to desactivate this. Beep is a Windows service and just like every other Windows services you can view and manipulate them via inline command:

```
net stop beep
```
  
<img class="aligncenter size-full wp-image-177" alt="net_stop_beep" src="http://geeksperiment.com/wp-content/uploads/2013/09/net_stop_beep-e13784567766422.png" width="550" height="151" srcset="http://geeksperiment.com/wp-content/uploads/2013/09/net_stop_beep-e13784567766422.png 499w, http://geeksperiment.com/wp-content/uploads/2013/09/net_stop_beep-e13784567766422-300x82.png 300w" sizes="(max-width: 550px) 100vw, 550px" />