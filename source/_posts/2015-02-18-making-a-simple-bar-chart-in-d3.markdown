---
layout: post
title: "Making a simple bar chart in d3 with animation"
date: 2015-02-18 14:07:38 +1100
comments: false
categories: ["javascript", "d3", "data visualisation"]
description: making a simple bar chart in d3 with transition
keywords: "d3, javascript, bar chart, bottom up, bottom down, data visualisation"
---


Although you can see lots of amazing charts in the home page of d3, d3 doesn't ship with any pre-built chart function.
In this post, I am going to draw a simple animated bar chart using d3.
The output will be:


<iframe width="100%" height="300" src="//jsfiddle.net/benedict_chan/rn8rdvt8/7/embedded/result,js,html,css/" allowfullscreen="allowfullscreen" frameborder="0">
</iframe>


<!-- more -->


Let's start by creating a simple bar chart, we will be creating it based on the following tutorial:
<a href="http://alignedleft.com/tutorials/d3/making-a-bar-chart" target="_blank">http://alignedleft.com/tutorials/d3/making-a-bar-chart</a>

The tutorial explains clearly on to how to make a simple bar chart, following is a fiddle I created, with just some modification as I preferred:
<iframe width="100%" height="300" src="//jsfiddle.net/benedict_chan/rn8rdvt8/1/embedded/js,result,html,css/" allowfullscreen="allowfullscreen" frameborder="0">
</iframe>


Note what have be added:
{% raw %}
``` js barchart.cs start:3 mark:3,9,10
var heightRatio = d3.max(dataSet) / canvasHeight;

var canvas = d3.select("#chart")
    .append("svg").attr("width", "100%")
    .attr("height", canvasHeight + "px")

var rectWidth = canvas.style("width").replace("px", "") / dataSet.length;
var barPadding = rectWidth / 5;
```
{% endraw %}


As an example, I am actually making the `scales` myself here, in pratice we should use d3's pre-defined <a href="https://github.com/mbostock/d3/wiki/Quantitative-Scales" target="_blank">scales</a> to help us on this.

## Adding animation to the chart

Too add animation in d3, we will use `transition`. To do this, it is as simple as to just assign an initial value to our chart attributes.
Then, we assign the final values (which is, what we already have) to the chart and applies transition.
The attributes that have to be animated are:
- `y` of the rectangles
- `height` of the rectangles


So, let's set an initial value to `0` of our `y` and `height`.
Therefore, we can keep our existing attributes of `x`, `width` and `fill`(color) of the chart. The code becomes:
{% raw %}
``` js bottomDownbarchart.cs start:18 mark:20,21,26-29
canvas.selectAll('rect')
.attr('x', function(d,i){return i*( rectWidth );})
.attr('y',  function(d){return 0;})
.attr("height", function(d){return 0;})
.attr("width", rectWidth - barPadding)
.attr("fill", function(d){ return "rgb(0, " + Math.floor(200 - d/d3.max(dataSet) * 100 )   +", 0)"; });

//Animate the chart
canvas.selectAll('rect')
.transition().duration(3000)
.attr('y',  function(d){return canvasHeight - d/heightRatio;})
.attr("height", function(d){return d/heightRatio;});
```
{% endraw %}

<iframe width="100%" height="300" src="//jsfiddle.net/benedict_chan/rn8rdvt8/6/embedded/result,js,html,css/" allowfullscreen="allowfullscreen" frameborder="0">
</iframe>

### Fixing the bottom down bar chart

Notice the animation we have is a bottom down chart. So, why is it not bottom up?
**It is because, the `y` start from `0` and increase downwardly.**
Therefore when we set your `y` to start from `0`, it's actually moving the bar downwardly too.
So in order to fix this, we should on the other hand, set `y` to it's largest value, which is, the `cavans height`!

{% raw %}
``` js bottomDownbarchart.cs start:18 mark:20
canvas.selectAll('rect')
.attr('x', function(d,i){return i*( rectWidth );})
.attr('y',  function(d){return canvasHeight;})
.attr("height", function(d){return 0;})
.attr("width", rectWidth - barPadding)
.attr("fill", function(d){ return "rgb(0, " + Math.floor(200 - d/d3.max(dataSet) * 100 )   +", 0)"; });

//Animate the chart
canvas.selectAll('rect')
.transition().duration(3000)
.attr('y',  function(d){return canvasHeight - d/heightRatio;})
.attr("height", function(d){return d/heightRatio;});
```
{% endraw %}

<iframe width="100%" height="300" src="//jsfiddle.net/benedict_chan/rn8rdvt8/7/embedded/result,js,html,css/" allowfullscreen="allowfullscreen" frameborder="0">
</iframe>

Now we have a bar chart animating bottom up!
