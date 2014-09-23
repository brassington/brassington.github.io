---
layout: post
title: 'Using Wercker to Setup CI Testing'
description: 'How to use a new service that uses docker containers to manage continuous integration testing.'
category: Javascript
tags: [Code, Testing, Services]
image:
  feature: 
comments: true
share: true
---
Continuous integration testing is an extremely useful tool. It allows you to check to see if a build is breaking everytime a developer pushes code. It helps your team keep your code in a good shape.

But this only works if you write tests for your code as you engineer your solution. I believe the right time to write a test for your code is immediately after you've finished a naive solutation and are asking yourself, does what I wrote work?

You should use unit tests to help you answer that question. Automating the answer to that question in code allows you to quickly assess what's working automatically, which is even better because it saves you and others so much time on the backend by spending a little extra time up front writing tests.

When you write tests immediately after you make an itial pass at a solution, tests also help communicate and document what you expected the inputs to be and what you expected the output to match. This is great for other developers who want to know how your code was intended to be used.

### Wercker

Wercker is a new service based out of the Netherlands that helps you test your code every time it's pushed to Github. If you use a rebase workflow with pull requests and code reviews, it's even more valuable, because it will can run all your tests on every suggested merge to the master branch. Very cool stuff.

Wercker uses Docker to accomplish some wonderful things. It's all backed by Github, so you can create environments, and scripts to run on those environments meant for particular purposes and then quickly share them with other developers solving the same problems.

### Werker Boxes and Steps

<img src="http://f.cl.ly/items/0x2f0q301u3q2J353t32/wercker_pipeline_box.png" />

Wercker separates test environments into two parts: the environment (boxes), and the commands you need to run on that environment (steps) to run your test suite. You just need to create a wercker.yml file in the repository you want to test, and then create a new application by following the prompts in Wercker.com's interface. It connects to your Github account and the repository you want to be tested, and finds the wercker.yml configuration file.

You configuration file should look something like this:

{% highlight javascript %}
{% raw %}
box: jabbrass/Rust-Cargo@0.0.5
build:
  steps:
    - jabbrass/rust-cargo-test@0.0.3
{% endraw %}
{% endhighlight %}

The first line is called a box and tells Wercker which environment to run your test suit in. You can browse a [whole list of these boxes](https://app.wercker.com/#explore/boxes/search/), and it even tells you what lines to enter in your configuration at the top of each description in the listing.

The line at the bottom, which is indented and has the dash in front of it (indentation matters here!), is a Step, this is a predefined shell script that will run on your repository's code once the environment is loaded and your code has been downloaded. You can find a [whole list of all those steps](https://app.wercker.com/#explore/steps/search/) that others are using, and in the listing copy and paste the code they show you need to add to your wercker.yml configuration file in your repository.


### Custom Wercker Boxes

A Wercker Box is just a repository on Github that has a wercker-box.yml file inside of it.

First, signup for Wercker, at Wercker.com.
Next, create a new application in the apps listing (if you can't find it, click on apps in the left menu).
Third, run through the configuration options, connect to Github, and add your repository, follow instructions to create Wercker box config.

Basically this amounts to specifying the bash commands that need to be run to setup the environment you need for testing. I recently set one of these up for a Rust box that you can find here: [Github Rust-Cargo Box](https://github.com/jabbrass/rust-cargo)

Now, you must go to the application on Wercker, find a successful build, or press the button to build for the first time, and then when the build turns green, then you should click deploy in the upper right hand corner, and set up a deploy to directory, and then deploy it. This will run all the commands to setup the environment, and then place the repository in the listing service on the sites for others to use. 

This workes essentially the same for steps, but you need to include a `run.sh` file along with a `wercker-step.yml` which is almost identical to the above configuration files.

For more documentation and help setting up Wercker, refer to their excellent documentation here: http://devcenter.wercker.com/articles/boxes/
