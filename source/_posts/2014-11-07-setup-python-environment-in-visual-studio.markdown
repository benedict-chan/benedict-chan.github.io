---
layout: post
title: "Setup Python (Anaconda) Environment in Visual Studio"
date: 2014-11-07 16:33:14 +1100
comments: false
categories: ["python", "anaconda"]
description: 
keywords: "setup python anaconda visual studio"
---

## Installing Python (Anaconda) ##

In the old days, setting up an python environment with packages like NumPy SciPy in Windows may takes several hours. This may include building NumPy or SciPy yourself.

Now, to save our day, we can actually just install them easily by some special distributions like:

- <a href="http://enthought.com/" target="_blank">EPD Free Enthought Python Distribution</a>
- <a href="http://continuum.io/" target="_blank">Anaconda Python</a>

Here, I am going to install Anaconda, simply download the Windows installer and you are done.


## Setup Visual Studio as your Python IDE ##

To use Visual Studio as your Python IDE, we can install the <a href="http://pytools.codeplex.com/" target="_blank" >Python Tools for Visual Studio (PTVS)</a>.


## Setup Python Environment - Anaconda ##

Now that we have installed (PTVS), we have to tell (PTVS) where to find our Python distributions, in this case, the one that Anaconda installed.
We can configu it from:

From Visual Studio -> Options -> Python Tools -> Environment Options

We can setup our new Anaconda environment like this:

{% img center /images/posts/2014-11-07-PythonEnvironmentOptions.jpg  %}

Once we have finished the setup, we can create a Python Project in Visual Studio, then try to run it our script using :

From Visual Studio -> Options -> Python Tools -> Execute Project in Python Interactive

