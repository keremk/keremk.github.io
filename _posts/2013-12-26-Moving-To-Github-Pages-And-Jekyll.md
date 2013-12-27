---
layout: post
title: Moving to Github Pages and Jekyll
description: "Some links and info on why I moved to using Github Pages as my blogging system."
modified: 2013-12-26
category: articles
tags: [blog, jekyll, github pages, markdown]
comments: true  
---

When I decided to start blogging again, I realized that my blogging engine of choice at the time, Wordpress, was aging. I was getting emails from Dreamhost that I should tend to my blog as it appeared to be hacked. On top of all that, the design of my site was aging and it needed to be updated to a [responsive design](http://coding.smashingmagazine.com/2011/01/12/guidelines-for-responsive-web-design/).

Instead of staying with Wordpress and Dreamhost, updating my designs and cleaning up the hacked pages, I decided to switch to an entirely new system. As I look for a new blog engine, I decided to move to a statically generated system. There are quite a few alternatives out there at the moment. Some of them are [node based](http://blog.bmannconsulting.com/node-static-site-generators) and some are [Ruby based](https://www.ruby-toolbox.com/categories/static_website_generation). Since Github pages support the popular Ruby based [Jekyll](http://jekyllrb.com) (which was originally built by Tom Preston-Werner anyways) natively, I decided to go with that. However if you are Javascript/Node.js fan, you may also want to try out the popular [Wintersmith](http://wintersmith.io/) as well. As a quick note, I also looked into using [Octopress](http://octopress.org/) as well, which is a bundling of some plugins, some convenient rake tasks and templating system for Jekyll. But for now, I decided to stay on Jekyll itself.

To set this up, there is a quick set of instructions on the [Github Pages](http://pages.github.com/) site. Or even better, someone just very recently wrote a [step by step instructions](http://24ways.org/2013/get-started-with-github-pages/) on how to set your system up with Jekyll and Github Pages. Note that Github pages support 2 types of web sites - Project and User/Organization. In this case, I set up a User/Organization site which is appropriate for a blog and you can only have one of those per Github account. BTW, did I already say that setting up Github Pages is free?

To create and edit content, I am using my favorite editor [Sublime Text](http://www.sublimetext.com/3) and a markdown previewer called [Marked](http://markedapp.com/). However I initially bumped into Marked in the Mac Appstore and purchased it from there. I then, realized that the one on the AppStore is an older version (1.3.x) and it is not updated there anymore. So in order to get proper syntax highlighting support as well as [many other great features](http://marked2app.com/), you need to purchase it from their web site. The markdown code block support on the Mac AppStore version of the app is unfortunately buggy. I am now testing their web site version. By the way, the markdown syntax on Github is slightly [different](https://help.github.com/articles/github-flavored-markdown). There is also a [great markdown cheatsheet](https://github.com/adam-p/markdown-here/wiki/Markdown-Cheatsheet), I use quite often. 

Finally, I wanted to pick a ready-made template for my site. These days, I really like the styles of [Medium](https://medium.com/) and [Svbtle](https://svbtle.com/). After a bit of googling around, I bumped into [this template](http://mmistakes.github.io/so-simple-theme/) and decided to make it my own.