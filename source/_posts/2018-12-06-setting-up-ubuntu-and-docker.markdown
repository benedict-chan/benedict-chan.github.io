---
layout: post
title: "Setting up Ubuntu and Docker"
date: 2018-12-08 10:38:22 +0000
comments: false
categories: ubuntu docker

description: "Setting up Ubuntu and Docker"
keywords: ubuntu, docker, ssh, xfce, xrdp
---

This post just listed some commands I used for setting up a testing Ubuntu machine and Docker.


### Install xfce using apt ###

		sudo apt-get update
		sudo apt-get install xfce4
		
### Install xrdp using apt ###

		sudo apt-get install xrdp
		sudo systemctl enable xrdp
		echo xfce4-session >~/.xsession
		sudo service xrdp restart

		# *Setting up a password for the rdp user*
		sudo passwd {username}


### Setting up Docker ###
		
		sudo apt-get install \
			apt-transport-https \
			ca-certificates \
			curl \
			software-properties-common
			
		curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -

		sudo add-apt-repository \
		   "deb [arch=amd64] https://download.docker.com/linux/ubuntu \
		   $(lsb_release -cs) \
		   stable"
		   
		sudo apt-get update

		sudo apt-get install docker-ce

### Setting up Docker Compose ###
		
		sudo curl -L https://github.com/docker/compose/releases/download/1.23.1/docker-compose-`uname -s`-`uname -m` -o /usr/local/bin/docker-compose
		sudo chmod +x /usr/local/bin/docker-compose
		
		

### Common methods for SSH under Windows ###

		# *Generating SSH key with a specific location*
		ssh-keygen -t rsa -b 2048 -C "{comment/email}" -f {KeyLocation}

		# *Copying the SSH public key*
		clip < {KeyLocation}.pub

		# *SSH using a key with a specific location*
		ssh username@hostip -i {KeyLocation}


### Setup Firefox ###

		sudo apt-get install firefox
