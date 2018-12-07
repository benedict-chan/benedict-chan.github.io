---
layout: post
title: "Use Docker to setup an environment for Octopress2"
date: 2018-10-18 10:23:39 +0000
comments: false
categories: octopress docker

description: "Use Docker to setup an environment for Octopress2"
keywords: "Octopress, Octopress2, Docker"
---

## Docker image for running Octopress 2##

Since this blog is made from Octopress 2 and it seems that Octopress has ended it's development after version 3.

In order to keep using it, it's better to create an development environment that I can keep on running it in the future.

The DockerFile for setting this environment is located [here](https://github.com/benedict-chan/dockers/tree/master/octopress2).

The Docker image is located in Docker Hub: [https://hub.docker.com/r/benedictchan/octopress2/](https://hub.docker.com/r/benedictchan/octopress2/).

### Running the environment ###

To simply use this Docker image, you can just mount your Octopress repository's source branch to the container's `app` folder:

`docker run -ti --rm -p 4000:4000 -v {source_path}:/app benedictchan/octopress2 `

<!-- more -->

#### Create new post ####

`rake new_post["title"]`

#### Generate the blog ####

`rake generate`

#### Preview the blog ####

`rake preview`

#### Publish the blog ####
`rake deploy`
