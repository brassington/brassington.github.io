---
layout: post
title: 'A Tour of Inheritance Patterns in Javascript: Part 1'
description: 'Find out how to use the functional class pattern in JS'
category: Javascript Classes
tags: [Code, Javascript]
image:
  feature: 
comments: true
share: true
---

I have encountered many people who believe that because Javascript has lacked a true blueprint-creating, property-copying class primitive, that it is underpowered in the inheritance department. This is simply untrue. Flexibility is the name of the game in JS. It gives you not one or two ways to share methods and properties, but four!

Each of these patterns really boil down to a matter of preference, but many developers/work-places choose one of these patterns to standardize their workflows. The most common choice, and most confusing because of its last-minute-addition-to-the-language status is pseudoclassical style which employes the `new` keyword. This is also the most complext of the four, many times considered the only real way to class in JavaScript.

### Functional Classes

First, it's easy to confuse a function in JS that adds properties to an existing object (sometimes called a decorator), with a functional class that builds the object that it's adding properties to. Function's can create an object first, then add properties and return that new object, making it easy to create many objects over and over by invoking the function.

I like to call these maker-functions or factories. They are functions which pump out objects with similar properties using the same interface. Many developers capitalize their class names like a proper noun.

The instance of the class is the object that was manufactured by that class: the resulting object, and creating an object using a class is often referred to as instantiating.

{% highlight javascript %}
{% raw %}
var makeRestaurant = function(address) {
  var instanceObj = {};

  instanceObj.address = address;
  instanceObj.isOpen = false;
  instanceObj.cashRegisters = 2;

  instanceObj.open = function() {
    this.isOpen = true;
  }

  return instanceObj;
}

var Subway = makeRestaurant('900 Mission St.');
Subway.open();
console.log(Subway.doors)  //-> true
{% endraw %}
{% endhighlight %}

This is a class in its simplest form. It creates objects, as many as desired. One problem with this method is that each function attached to the object needs it's own memory. 

We can optimize this slightly by defining a function outside the maker-function, and adding a reference on the class to the other named function. This way, we only have a reference to the single function defined in one place and occupying a place in memory once:

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

The above is not best practice. It creates another global scope variable, and could potentially add many additional functions unattached to the restaurant class which is associated.

When we return to part two of this series, we'll take a closer look at the functional-shared pattern, the most basic of which I've demonstrated above with one shared globally-scoped method (another name for a function associated with a class).

See you next week!
