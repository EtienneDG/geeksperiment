---
id: 482
title: First data driven map with OpenLayer
date: 2014-05-07T17:56:34+00:00
author: Etienne
excerpt: A lot of efforts are put to display data in a meaningful ways, for instance on an inforgraphy, on a dynamic graph or even on a map. In addition, "Big data" is everywhere (at least it seems like it) so when you come across a dataset, which you want to extract meaning from, a lot of tools are available.
layout: post
guid: http://geeksperiment.com/?p=482
permalink: /first-data-driven-map-with-openlayer/
categories:
  - Projects
  - Uncategorized
tags:
  - heatmap
  - javascript
  - map
  - tutorial
---
A lot of efforts are put to display data in meaningful ways, for instance on an inforgraphy, on a dynamic graph or even on a map. In addition, &laquo;&nbsp;Big data&nbsp;&raquo; is everywhere (at least it seems like it) so when you come across a dataset, which you want to extract meaning from, a lot of tools are available.

The main goal was to display my dataset on a map. After cleaning up and formating the data set I was wondering how to display my 60 thousands + records. Since I got GPS coordinates I didn&rsquo;t wonder that long&#8230;

<span style="font-size: large;"><a href="#tldr"><strong>>>>TL;DR</strong></a></span>

There a solutions for adding a interactive map on a page, the principal ones being:

  * OpenLayer : an open map tool, based on OpenStreetMap (the maps-equivalent of wikipedia)
  * Google Maps
  * ArcGis

## Generalities on maps

[<img class="alignleft size-full wp-image-484" src="http://geeksperiment.com/wp-content/uploads/2014/05/map_layers-e13994058768602.jpg" alt="map_layers" width="399" height="342" srcset="http://geeksperiment.com/wp-content/uploads/2014/05/map_layers-e13994058768602.jpg 399w" sizes="(max-width: 399px) 100vw, 399px" />](http://geeksperiment.com/wp-content/uploads/2014/05/map_layers-e13994058768602.jpg)To define how dynamics web maps works the simplest way is to drill down from the map to tiniest of its element. Basically a map is a pile of layers. Each layers displays a different type of information, such as borders, elevation, land,etc. A layer is composed of a grid of tiles. The tiles are images that are served with a mapping scheme to create the grid. So when you call different map APIs, the server return the layer as a series of tiles assembled.

The three solutions (Google, OSM and ArcGis) work that way. That structure allows a great deal of customization.

To fully understand how maps work, its good to have a little knowledge on the different projection systems. I won&rsquo;t talk about them, [wikipedia](http://en.wikipedia.org/wiki/Map_projection) is quite thorough on that point.

&nbsp;

## Adding a map to a page

I chose OpenLayer because its open source (I used to contribute to OpenStreetMap 😉 ) and because it&rsquo;s the base to LOTS of JS frameworks. To add an OpenLayer map on a page, you just need to reference the OpenLayer JS, add a container that  will welcome your map. Here is the minimum code to display a simple map on a page:

&nbsp;

  ```html
  <!DOCTYPE html>

<html>
    <head>
        <meta http-equiv="Content-Type" content="text/html; charset=utf-8">
        <title>Adding a simple map</title>
        <style>
            #myMap {
                height: 800px;
            }
        </style>
    </head>
    <body>
        <div id="myMap">
        </div>
        <script src="http://openlayers.org/api/OpenLayers.js"></script>
        <script type="text/javascript">
            var map;
            function init(){
                //defining the projection and adding a map to an element
                var sphericalMercatorProj = new OpenLayers.Projection("EPSG:900913");
                var geographicProj = new OpenLayers.Projection("EPSG:4326");
                map = new OpenLayers.Map("myMap");

                //adding layers
                //only adding the default OSM layer here
                map.addControl(new OpenLayers.Control.LayerSwitcher());
                mylayer = new OpenLayers.Layer.OSM();
                map.addLayer(mylayer);

                //Next section"s code goes here...
                //final display settings go here
                var mapCenter = new OpenLayers.LonLat(2.0,46.0);
                map.setCenter(mapCenter.transform(
                    new OpenLayers.Projection("EPSG:4326"), 
                    map.getProjectionObject()),6);
            };

            window.onload = function() {
                init(); 
            };
        </script>
    </body>
</html>
```

So far, I have a simple map with the default OSM layer (centered on France) and thats about it.

&nbsp;

## Showing data on the map

The easiest way to integrate data is to have your dataset ready as a JSON. The one I used looks like this:

```
  data = [{lat : 45.0 , lon : 2.0, value: 100},
          {lat : 42.134 , lon : 1.990, value: 50},
          ...]
```

Having coordinates you can easily display points on the map adding this code to the previous javascript code:

```javascript

//Loading data
//size (width,height) of the icon in pixels
var size = new OpenLayers.Size(21,25);
var offset = new OpenLayers.Pixel(-(size.w/2), -size.h);

//the offset is used to place the base of the pin on the actual coordinates
//if no offest defined, the center of the pin will be placed on the coordinates
var icon = new OpenLayers.Icon("http://www.openlayers.org/dev/img/marker.png", size, offset);

$.ajax({
    "async": true,
    "global": false,
    "url": "./data/your_dataset.json",
    "dataType": "json",
    "success": function (data){
                          var dataLength = data.length;
                          console.log(dataLength);
                          //iterating through each point
                          while(dataLength--){
                            markers
                            .addMarker( new OpenLayers.Marker(
                                  new OpenLayers.LonLat(data[dataLength].lon, data[dataLength].lat)
                            .transform(new OpenLayers.Projection("EPSG:4326"), map.getProjectionObject()), icon.clone()));
        }
    }});
```

For example this is what it looks like with a couple hundreds of points:

[<img class="alignleft size-full wp-image-494" src="http://geeksperiment.com/wp-content/uploads/2014/05/markers_example2.png" alt="markers_example" width="813" height="607" srcset="http://geeksperiment.com/wp-content/uploads/2014/05/markers_example2.png 813w" sizes="(max-width: 813px) 100vw, 813px" />](http://geeksperiment.com/wp-content/uploads/2014/05/markers_example2.png)

&nbsp;

## REALLY showing data on the map

Actually I just added points on the map, but these points don&rsquo;t show my actual data, the &laquo;&nbsp;value&nbsp;&raquo; attribute of my JSON objects. What I want is to have weighted points according to their &laquo;&nbsp;value&nbsp;&raquo;. A fun way to do that is with a heatmap: depending on the value the area around the point will be warmer (red) or colder (blue). Basically, I will just add a layer containing the data as a heat distribution.

I used [heatmap.js](http://www.patrick-wied.at/static/heatmapjs/) to do so. Quite simple to use, hardboiled code available here:<a href="http://www.patrick-wied.at/static/heatmapjs/" target="_blank">http://www.patrick-wied.at/static/heatmapjs/</a> . The result, with 63k+ points looks like that

[<img class="alignleft size-full wp-image-500" src="http://geeksperiment.com/wp-content/uploads/2014/05/heatmap_final2.png" alt="heatmap_final" width="780" height="639" srcset="http://geeksperiment.com/wp-content/uploads/2014/05/heatmap_final2.png 780w" sizes="(max-width: 780px) 100vw, 780px" />](http://geeksperiment.com/wp-content/uploads/2014/05/heatmap_final2.png)

Pretty neat! 🙂

&nbsp;

<div id="tldr">
</div>

## TL;DR Section

  * Create a map: 
      * JSON with gps coord.
      * add http://openlayers.org/api/OpenLayers.js to your html
      * add a container for the map
      * add [script](https://gist.github.com/EtienneDG/6169df5c914ea105dc73)
  * Adding heat layer: 
      * add http://openlayers.org/api/OpenLayers.js ; heatmap.js and heatmap-openlayers-renderer.js
      * boilerplate code [here](http://www.patrick-wied.at/static/heatmapjs/example-heatmap-openlayers.html)