---
layout: post
title: "Adding simple SEO for octopress"
date: 2014-01-29 10:34:19 +1100
comments: false
categories: ["Octopress, SEO"]
keywords: "Octopress, SEO, Meta Tag"
description: "Adding simple SEO for octopress"
---

## Adding Meta Tag, Keywords and Descriptions for your Octopress Blog ##
I just started using Octopress, seems it is quite simple to setup. However, the default template doesn't provide the fields for your site, your post, or your pages.
After a google search, seems it is quite easy to setup.

## The main Octopress Site ##
##### Description #####
Meta tag `Description` is aleady in config file `_config.yml`. However, to show it in the main site. You have to modify the file `source/_includes/head.html`.
{% raw %}
``` ruby source/_includes/head.html start:10 mark:10,11
	-{% capture description %}{% if page.description %}{{ page.description }}{% else %}{{ content | raw_content }}{% endif %}{% endcapture %}
	+{% capture description %}{% if page.description %}{{ page.description }}{% elsif site.description %}{{ site.description }}{% else %}{{ content | raw_content }}{% endif %}{% endcapture %}
	<meta name="description" content="{{ description | strip_html | condense_spaces | truncate:150 }}">
```
{% endraw %}
## New Post ##


## Reference ##
- <a href="http://xit0.org/2013/05/seo-for-octopress-websites/" target="_blank">SEO for Octopress Websites</a>
