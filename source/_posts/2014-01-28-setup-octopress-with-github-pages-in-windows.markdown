---
layout: post
title: "Setup Octopress with Github pages in Windows"
date: 2014-01-28 14:11:37 +1100
comments: false
categories: Octopress

keywords: "Octopress, Windows, Github, Setup, Howto"
description: "Setup Octopress with Github pages in Windows"
---

## Software ##
1. Install [Git](http://git-scm.com/), [GitHub for Windows](http://windows.github.com)
2. Install [Ruby Installer for Windows](http://rubyinstaller.org/downloads/)
3. Install [Ruby Development Kit](https://github.com/oneclick/rubyinstaller/downloads/)
, extract this to a folder like `C:\RubyDevKit`

## Setup Ruby ##
	cd C:\RubyDevKit
	ruby dk.rb init
	ruby dk.rb install

## Setup Octopress ##
#### Go to your source folder and clone Octopress ####
	git clone git://github.com/imathis/octopress.git username

#### Install ruby's bundler ####
	cd username
	gem install bundler
	bundle install

## Setup Github repository and pages ##
Create a new repository named: `username.github.io` in github

#### Fix hellip in Windows ####
In order to generate our first Octopress templates, we have to modify the `Rakefile`, just remove `&hellip;` 
``` ruby Rakefile start:348 mark:349
    system "git init"
	system "echo 'My Octopress Page is coming soon' > index.html"
	system "git add ."
```
You can then run
	rake setup_github_pages
This will ask your github repository url and add your repository as the default origin, you can check this after by
	git remote -v

#### Config the blog ####
Just head to the file <a href="http://octopress.org/docs/configuring/"><code>_config.ym</code></a>

## Create your site ##
	rake generate

## Preview it locally ##
	rake preview
It should now be shown in <a href="http://localhost:4000" target="_blank">http://localhost:4000</a>

## Start a new Post ##
	rake new_post["New Post"]

## Deploy to github ##
	rake deploy
This will generate your copy and copied to `_deploy` folder.

## Commit your source too ##
	git add .
	git commit -m 'your message'
	git push origin source

## Reference ##
- <a href="http://www.techelex.org/setup-octopress-on-windows7/" target="_blank">Setup Octopress on Windows7</a>
- <a href="http://derantell.github.io/blog/2012/12/02/getting-started-with-octopress-on-windows/" target="_blank">Getting Started With Octopress on Windows</a>
- <a href="http://octopress.org/docs/blogging/code/" target="_blank">Sharing Code Snippets</a>
