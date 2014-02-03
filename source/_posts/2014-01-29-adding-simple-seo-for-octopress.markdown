---
layout: post
title: "Adding simple SEO for octopress"
date: 2014-01-29 10:34:19 +1100
comments: false
categories: ["Octopress", "SEO"]
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
``` rb source/_includes/head.html start:10 mark:10,11
-{% capture description %}{% if page.description %}{{ page.description }}{% else %}{{ content | raw_content }}{% endif %}{% endcapture %}
+{% capture description %}{% if page.description %}{{ page.description }}{% elsif site.description %}{{ site.description }}{% else %}{{ content | raw_content }}{% endif %}{% endcapture %}
<meta name="description" content="{{ description | strip_html | condense_spaces | truncate:150 }}">
```
{% endraw %}
##### Keywords #####
We have to add meta tag `keywords` is in config file `_config.yml` ourselves. We also need to modify the file `source/_includes/head.html` again for this change.
{% raw %}
``` rb _config.yml start:10 mark:11
description: Site/Blog description
+keywords: "siteKeywords"
```
{% endraw %}
{% raw %}
``` rb source/_includes/head.html start:12 mark:12,13,14
-{% if page.keywords %}<meta name="keywords" content="{{ page.keywords }}">{% endif %}
+{% capture keywords %}{% if page.keywords %}{{ page.keywords }}{% elsif site.keywords %}{{ site.keywords }}{% endif %}{% endcapture %}
+<meta name="keywords" content="{{ keywords | strip_html | condense_spaces }}" />
```
{% endraw %}
## New Post ##
The default template for creating new post or pages doesn't provide you the meta tag `Description` and `keywords`. We can add it in the RakeFile.
{% raw %}
``` rb Rakefile start:92 mark:114,115
# usage rake new_post[my-new-post] or rake new_post['my new post'] or rake new_post (defaults to "new-post")
desc "Begin a new post in #{source_dir}/#{posts_dir}"
task :new_post, :title do |t, args|
  if args.title
    title = args.title
  else
    title = get_stdin("Enter a title for your post: ")
  end
  raise "### You haven't set anything up yet. First run `rake install` to set up an Octopress theme." unless File.directory?(source_dir)
  mkdir_p "#{source_dir}/#{posts_dir}"
  filename = "#{source_dir}/#{posts_dir}/#{Time.now.strftime('%Y-%m-%d')}-#{title.to_url}.#{new_post_ext}"
  if File.exist?(filename)
    abort("rake aborted!") if ask("#{filename} already exists. Do you want to overwrite?", ['y', 'n']) == 'n'
  end
  puts "Creating new post: #{filename}"
  open(filename, 'w') do |post|
    post.puts "---"
    post.puts "layout: post"
    post.puts "title: \"#{title.gsub(/&/,'&amp;')}\""
    post.puts "date: #{Time.now.strftime('%Y-%m-%d %H:%M:%S %z')}"
    post.puts "comments: true"
    post.puts "categories: "
    post.puts "description: "
    post.puts "keywords: "
    post.puts "---"
  end
```
{% endraw %}

## Reference ##
- <a href="http://xit0.org/2013/05/seo-for-octopress-websites/" target="_blank">SEO for Octopress Websites</a>
