---
layout: post
title: 'A Tour of Inheritance Patterns in Javascript: Part 4'
description: 'Find out how to use the most popular pseudo-classical class pattern in JS'
category: Javascript Classes
tags: [Code, Javascript]
image:
  feature: 
comments: true
share: true
---

Pseudo-classical instancing in JavaScript is extremely commonplace in the industry. Most browsers have optimized for this classing technique, and so most developers depend on it without investigating the other options, as we've discussed in the previous installments.

This pattern emulates the syntactic sugar of many other class-based languages. It does not, however, behave the same way as other languages' classing. It uses the exact same constructs as the prototypal inheritance with some strange plot twists.

### Pseudo-classical Classes

We left off last time with the following code snippet creating restaurant instances for us:

{% highlight javascript %}
{% raw %}
var Restaurant = function(address) {
  var instanceObj = Object.create(Restaurant.prototype);

  instanceObj.address = address;
  instanceObj.isOpen = false;
  instanceObj.cashRegisters = 2;

  return instanceObj;
}

Restaurant.prototype = {
  open: function() {
    this.isOpen = true;
  },
  close: function() {
    this.isOpen = false;
  }
}

var subway = Restaurant('878 Main St.');
{% endraw %}
{% endhighlight %}

We've written a lot of code here. It would be nice to have a shorter, more terse syntax for expressing what we've written. If our javascript interpreter inserted a few lines for us, we could type less. We could remove the 'var instanceObj = Object.create(Restaurant.methods)' and the 'return instanceObj', because those will be repeated everytime we create a class. But we still need these lines to be done, so the authors of JS decided that they would auto-insert those lines for us.

{% highlight javascript %}
var Restaurant = function(address) {
  //var instanceObj = Object.create(Restaurant.prototype);
  // Now removed and replaced with this invisible line inserted at run-time (new keyword)
  // this = Object.create(Restaurant.prototype)

  instanceObj.address = address;
  instanceObj.isOpen = false;
  instanceObj.cashRegisters = 2;

  //return instanceObj;
  // JS replaces with this with the following line inserted at run-time (new keyword)
  //return this;
}

Restaurant.prototype = {
  open: function() {
    this.isOpen = true;
  },
  close: function() {
    this.isOpen = false;
  }
}

var subway = new Restaurant('878 Main St.'); // Added new keyword to do the auto-insertion
{% endhighlight %}

We saved a few lines of typing, and invoked the Restaurant function with the `new` keyword. As you may have already guessed, the `new` keyword is the culprit responsible for auto-inserting lines on particular functions. The surprising part of this syntax, is that you can use the `new` keyword infront of any function invocation; it will not stop you. This means you need to be extremely careful that you only use the `new` keyword when you need those two lines auto-inserted.

Now we just need to update the references to the object that's being created, and use `this`, which is the object created at run-time for us, but using the `new` keyword before invoking this function.

{% highlight javascript %}
{% raw %}

  this.address = address;
  this.isOpen = false;
  this.cashRegisters = 2;

{% endraw %}
{% endhighlight %}

And that's it! Really and truly, the mechanism is functionally the same as the previous prototypal class pattern. It just made it a little easier.

Now subclassing the pseudoclassical class pattern is super confusing because the interpreter has been doing some auto-insertion behind our backs (not in our literal code). We'll cover subclassing in the next series.

Thanks for learning about classing patterns in JavaScript with me, if you liked this series, let me know! Feel free to contact me with an questions: