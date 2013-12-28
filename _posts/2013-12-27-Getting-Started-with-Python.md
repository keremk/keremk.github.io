---
layout: post
title: Getting Started with Python and Data Science
description: "Setting up a Python development environment for data science work."
modified: 2013-12-27
category: articles
tags: [python, ipython, repl, pandas, data science]
image:
  feature: ball-python.jpg
  credit: Ball Python
  creditlink: http://www.ballpythonaddiction.com/
comments: true  
---

A week or so ago, I had to come up with a solution to export a cleaner version of our data stored in our application database. We are using Postgres as our database and making use of the [JSON storage](http://clarkdave.net/2013/06/what-can-you-do-with-postgresql-and-json/) and [HSTORE key-value storage](http://www.postgresql.org/docs/9.0/static/hstore.html) for our application needs. But when it comes to exporting our data for analysis, having columns with JSON and HSTORE encoded key-value pairs is not convenient. So I decided to take the opportunity to dive into using Python. After all, Python is quickly becoming [the de-facto language for data science](http://www.r-bloggers.com/the-homogenization-of-scientific-computing-or-why-python-is-steadily-eating-other-languages-lunch/). 

However I am a noob when it comes to Python. As such, this blog post is intended for noobs like me. I will try to explain how to setup a development environment for Python and give some tips on where to get started in learning Python for data science. I am also assuming that this is not your first language and you are probably a professional programmer who is fluent in one-or-more languages, and good with the command-line. If so, let's start:

## What to install?

When you start googling around, you will run into many alternatives. Your first decision is going to be around using Python 2.7 or 3.3, which turns out to be not backwards compatible. Yes, it definitely sounds like [Python 3.3 is better than 2.7](https://speakerdeck.com/pyconslides/python-3-dot-3-trust-me-its-better-than-python-2-dot-7-by-dr-brett-cannon) and after 5 years it is probably time to move. Looks like most popular packages including the machine learning package [scikit-learn](https://github.com/scikit-learn/scikit-learn/commit/22dbecccff5b856d1db0c97310f542fbc5df4f64) has made the move to Python 3.3+ this year, so we should be good. 

The second decision is in choosing a package manager/distribution. The most common choice is to use [PIP](https://pip.readthedocs.org/en/latest/). But as I was searching around, I found out about another alternative specifically geared towards scientific computing. It is called [Conda](http://www.continuum.io/blog/conda) and it sounded like a pretty convenient way to quickly setup my system with a set of data science related packages. Even though the default installation comes with 2.7, they also support [Python 3.2+](http://continuum.io/blog/anaconda-python-3). Also once you start using Conda, you don't have to worry about leaving behind PIP. Apparently Conda falls back to PIP, if it can not find the distribution package you are looking for. [This Stackoverflow thread](http://stackoverflow.com/questions/18640305/how-to-keep-track-of-pip-installed-packages-in-an-anaconda-conda-env) has more on that.

The third decision is to figure out your development environment, text editor to use etc. I am a big fan of [Sublime Text](http://www.sublimetext.com/3) and I decided to keep using that. My terminal choice is [iTerm](http://www.iterm2.com/#/section/home), shell choice is [zsh](https://github.com/robbyrussell/oh-my-zsh) and finally as the debugger/REPL environment, I settled on [iPython](http://ipython.org/). More on that later... 

If you like IDEs better, Jetbrains has a version of their IDE for Python development, called [PyCharm](http://www.jetbrains.com/pycharm/).There is a free community edition as well as a paid one, which comes with various framework support including Django etc. I also recently bumped into a brand new OSX native Python IDE that claims to be inspired by XCode called [Exedore](http://celestialteapot.com/exedore/). I have not had a chance to try it out myself, but if you are interested, there is a free trial.

[Anacondo distribution](http://continuum.io/downloads) contains all the interesting packages as well as the iPython and iPython Notebook. It is free and probably the most straight forward way to get started. I picked the GUI package installer amongst the choices for Mac OSX. When you first install it, you need to set your path, so that it reads from the ```anaconda/bin``` path instead. 

## [REPL](http://en.wikipedia.org/wiki/Read%E2%80%93eval%E2%80%93print_loop) Environment - iPython

Being used to the Ruby goodies, including the Ruby REPL called [pry](http://pryrepl.org/), my first instinct is to look for a good REPL environment. And iPython delivers on that. Especially when learning a new language as well as testing out some quick thoughts, [REPL driven development](https://speakerdeck.com/conradirwin/repl-driven-development-with-pry) is very productive. Plus you can also use iPython for debugging your scripts.

Let's say, you have a simple Hello World script like below in file ```hello_world.py```

{% highlight python linenos %}
      from IPython.core.debugger import Tracer; breakpoint = Tracer()
    
      output = "Hello World"
      breakpoint()
      print output
{% endhighlight %}

Then if you open up your ipython shell, and call the magic command ```%run hello_world.py```, you will stop at the breakpoint you defined on line 5:

{% highlight %}
      In [5]: %run hello_world.py

      > /Users/foo/PyTest/hello_world.py(5)<module>()

            3 output = "Hello World"
            4 breakpoint()
      ----> 5 print output
{% endhighlight %}

To learn more about iPython, you can read the [tutorial](http://ipython.org/ipython-doc/dev/interactive/tutorial.html) on the official iPython site.

## iPython Notebook

Going through iPython documentation, I also bumped into another great tool called the iPython Notebook. Let's say you come up with a cool analysis of your data and you would like to share the code, the math behind it, inline explanation of what your code does and finally the full output of running your code, including graphs. That is where iPython Notebook comes handy. You can use the [public viewer](http://nbviewer.ipython.org/) to share your notebooks with other people if you don't mind it being available publicly. This is especially great for public presentations, where you can go through your code and its output and explain it to your audience and then make it available to them after your presentation. You can also set up a private [internal server](http://nbviewer.ipython.org/github/Unidata/tds-python-workshop/blob/master/ipython-notebook-server.ipynb) as well. If you like to host it in the cloud instead, there are some good [step-by-step instructions](https://gist.github.com/iamatypeofwalrus/5183133) on how to do that on AWS. Or you can try out some 3rd party [hosted](http://www.continuum.io/wakari) [solutions](http://www.picloud.com/) for privately sharing your notebooks.

To get this setup on your computer, all you need to do is to run ``` ipython notebook ``` which starts a new server locally and fires up  your browser. In the browser session, you can then create new notebooks, load existing ones and save them for later usage. The notebook file format is a single json with the .ipynb extension. To share your notebooks publicly, all you need to do is to create a new public gist on Github and copy its GistID to the page you see at [http://nbviewer.ipython.org](http://nbviewer.ipython.org). You can also convert your notebooks to other formats using the [nbconvert tool](http://ipython.org/ipython-doc/stable/interactive/nbconvert.html#nbconvert) for non-interactive viewing.

## Learning Python

Now that, you set up your environment and learned how to share your code with your colleagues and friends, it is now time to do some actual coding. There is a list of learning python sources on the [python.org](https://wiki.python.org/moin/BeginnersGuide/Programmers) wiki, but my favorite is the [Google Python Class](https://developers.google.com/edu/python/). Another even shorter intro for experienced developers is [10-minute Intro to Python](http://www.stavros.io/tutorials/python/).

Once you get a quick grasp of Python, if you are doing some data science work, I highly recommend [this tutorial](http://www.gregreda.com/2013/10/26/intro-to-pandas-data-structures/) for using [Pandas](http://pandas.pydata.org/). After reading that tutorial and once you get used to using iPython and iPython Notebook, you will probably stop using Excel for many of your basic data manipulations.  

If you have access to the [Safari Books Online Bookshelf](https://ssl.safaribooksonline.com/trial), I would also recommend adding [Python for Data Analysis](http://shop.oreilly.com/product/0636920023784.do) and [Programming Collective Intelligence](http://shop.oreilly.com/product/9780596529321.do) to your bookshelf to get going with Data Science using Python. Another really cool resource is an online book called [Probabilistic Programming and Bayesian Methods for Hackers](http://nbviewer.ipython.org/github/CamDavidsonPilon/Probabilistic-Programming-and-Bayesian-Methods-for-Hackers/blob/master/Prologue/Prologue.ipynb). The whole book is interactive and is rendered with the iPython Notebook Viewer!

And one final tip, if you are looking for an interactive alternative to [Matplotlib](http://matplotlib.org/), check out the up-and-coming library [Bokeh](http://bokeh.pydata.org/).


