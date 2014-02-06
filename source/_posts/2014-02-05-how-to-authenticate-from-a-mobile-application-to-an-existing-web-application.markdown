---
layout: post
title: "How to authenticate from a mobile application to an existing web application"
date: 2014-02-05 16:35:51 +1100
comments: true
categories: ["mobile", "authentication"]
description: 
keywords: "mobile, authentication"
---


## The problem ##
The experience for a user authenticating to a web site vs a mobile application is totally different.

### The web experience ###
1. User comes to a web site
2. User got redirected to the Login Page if needed
3. User login by using their UserId,Password (cookie is created)
4. User logout or leave the browser (cookie may expire) 
5. User come to the web applicaton next time, may need to login again (if cookie expired)

### The mobile app experience ###
1. User open the app for the first time, asked login with their UserId,Password
2. After that, in most cases, user access to the app assuming they are always logged in and never need to logout or login again.


## The solution ##

<!-- more -->

### Create an API for the mobile to authenticate an user ###
An API call is needed for the mobile app to authenticate an user by UserId/password. 
For security reason, it should connect via a SSL secure connection. 

### Issue a token to the mobile from the API when the user logins successfully ###
For security reason, we should never save a user password in the mobile app, therefore, what we do is to issue a token which acts like a never expired cookie as in a web application.

#### About the token ####
When creating this authentication token, we should remember that a user may have more than one device installing our mobile application.
Therefore 
1. The relationship for a user to a token is not a one-to-one but one-to-many relationship.
2. The token should have a corresponding timestamp when created because in case a user changed their password, all token issued before this password changing time should be expired


### Calling other Web Application's API  ###
Your mobile application should now use this token to call your server-side APIs. Your server-side should validate this token as a similair way as how to validates a login cookie.
One way to pass this token, is to send it throught HTTP headers instead of a URL query parameters.


