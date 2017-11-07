---
id: 512
title: 'Adaptive-Background : apply predominant color from image'
date: 2014-05-09T11:40:29+00:00
author: Etienne
excerpt: Great javascript tool to extract predominant color of a picture.
layout: post
guid: http://geeksperiment.com/?p=512
permalink: /adaptive-background-apply-predominant-color-from-image/
categories:
  - Tips and tricks
tags:
  - javascript
  - sharing
  - tips
format: aside
---
Just wanted to share this small tool developped by _Brian Gonzalez_, because it&rsquo;s pretty cool and useful: <http://briangonzalez.github.io/jquery.adaptive-backgrounds.js/> .

The extraction of the predominant color is done by looping through each pixel of the image, and creating an associative array where the keys are the RGB strings and the value the count of occurences of the color. The down side for this process is that it can be a bit long for large images&#8230;

I stumbled upon it a few months ago, but never used it until my [last post](http://geeksperiment.com/first-data-driven-map-with-openlayer/ "First data driven map with OpenLayer"). My body background being an image, if my posts are too long I get an awful white background at the end of the image:[<img class="aligncenter size-large wp-image-515" src="http://geeksperiment.com/wp-content/uploads/2014/05/background-fail2-1024x308.png" alt="background-fail" width="900" height="270" srcset="http://geeksperiment.com/wp-content/uploads/2014/05/background-fail2-1024x308.png 1024w, http://geeksperiment.com/wp-content/uploads/2014/05/background-fail2-300x90.png 300w, http://geeksperiment.com/wp-content/uploads/2014/05/background-fail2.png 1105w" sizes="(max-width: 900px) 100vw, 900px" />](http://geeksperiment.com/wp-content/uploads/2014/05/background-fail22.png)

Felt like a good opportunity to test adaptive-background.

For some reason the snippet to add to the page, won&rsquo;t work when added to the footer/header template of the theme so I added it directly in the post:

```javascript  
<script src="..js/jquery.adaptive-backgrounds.js"></script>
<script>
    jQuery(document).ready(function($){
          $.adaptiveBackground.run()
        });

</script>
```

Works great, I&rsquo;m keeping this in mind for a next (better) use&#8230; I did finally change the css property of my body background <img src="http://geeksperiment.com/wp-includes/images/smilies/rolleyes.png" alt=":roll:" class="wp-smiley" style="height: 1em; max-height: 1em;" />

&nbsp;