---
layout: post
title: "Wordpress to Jekyll: Almost Done"
description: This weekend I migrated my blog to simple, minimal, and sleek Jekyll
headline: 
category: Blog-Maintenence
tags: [Jekyll, Features, Migration]
image: 
  feature: jekyllLogo.png 
comments: true
mathjax: 
---
Jekyll is a Ruby static site generator which creates minimalist, beautiful, and responsive blogs. It's designed to be used by programmers and anyone who wants to focus on writing great content in Markdown instead of spending all day playing with backend features, though it has plenty of features to be sure. 

I migrated my blog to Jekyll because my previous host was a pain to deal with, and now I can host the whole thing using Github. It was a relatively painless process, more or less, and only took a few hours. And I enjoy the workflow using source control, because I have to use it as a developer every day any way.

As a developer, I spend most of my day in a text editor, so it makes sense to be able to just type my posts write inside the same editor!

The tricky part was choosing what theme to use and how to develop locally without committing constantly to see changes. I highly recommend using the advice from [jekyllrb.com](http://jekyllrb.com/docs/github-pages/). It suggests that you should set the baseurl option in your _config.yml to your /project-name – with a leading slash and no trailing slash.

Then use jekyll serve --baseurl '' when generating your site locally. It keeps you from needing to change the config every time you swich between developing online / on your own machine.

I'll let you know as I go if I have any other issues, but so far Jekyll's been simple, and a delight to work with. I highly recommend it.

Now I just need to finish adding my old posts.