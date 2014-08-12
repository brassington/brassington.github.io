---
layout: post
title: 'A Tour of Inheritance Patterns in Javascript: Part 2'
description: 'Find out how to use the functional-shared class pattern in JS'
category: Javascript Classes
tags: [Code, Javascript]
image:
  feature: 
comments: true
share: true
---

Continuing where I left off last time, JavaScript has many different ways to create classes and instances of those classes. Today we'll examing functional-shared in more detail. But why would we use functional-shared over functional?

Functional-shared classes help us use less memory by sharing the same methods across many instances of our class. The functional style gives each instance it's own properties, copying them into every single new instance. We would prefer to have each of our new instances share a reference to one definition of the methods available to our functions.

### Functional-Shared Classes

Again, many engineers would argue that functional classes in JavaScript are ineffective / inefficient. Really, it's a matter of preference, and you may work in an environment where the style preferences differ from using this style of classing.

Here is the example from the last post:

{% highlight javascript %}
{% raw %}
var makeRestaurant = function(address) {
  var instanceObj = {};

  instanceObj.address = address;
  instanceObj.isOpen = false;
  instanceObj.cashRegisters = 2;

  instanceObj.open = open; // <- This is the reference to the open function below.

  return instanceObj;
}

var open = function() {
  this.isOpen = true;
}
{% endraw %}
{% endhighlight %}

You can see that the method, `open`, is defined below the makeRestaurant function which returns constructed objects.

This is one way to do functional-shared, but its downfall is that you end up with a lot of free-floating functions which clutter the space below the maker function. Wouldn't it be nice if we could group our shared methods? We can!

In an object, we could define multiple methods, and then use our maker function to copy the properties out of the object and place them in the new object created by our maker function (class).

If we use a library like underscore or lo-dash, we can quickly copy properties by reference to the new restaurant instances.

{% highlight javascript %}
{% raw %}
var makeRestaurant = function(address) {
  var instanceObj = {};

  instanceObj.address = address;
  instanceObj.isOpen = false;
  instanceObj.cashRegisters = 2;

  _.extend(instanceObj, methodsObj);

  return instanceObj;
}

var methodsObj = {
  open: function() {
    this.isOpen = true;
  },
  close: function() {
    this.isOpen = false;
  }
}

// To create an instance, call your maker function. It will now contain the same methods by reference everytime you create another instance.
var subway = makeRestaurant('878 Main St.');
{% endraw %}
{% endhighlight %}

This is easier, avoids extra typing every time you add a new method, and keeps them all in the same packaged object so that if you ever need to make changes, they will be copied by reference into all of the instances, saving memory and reducing complexity.

Next week, I'll cover prototypal classing: one of the most underutilized methods of classing in JavaScript!