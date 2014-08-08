---
layout: post
title: 'A Tour of Inheritance Patterns in Javascript: Part 3'
description: 'Find out how to use the prototypal class pattern in JS'
category: Javascript Classes
tags: [Code, Javascript]
image:
  feature: 
comments: true
share: true
---

Prototypal classing is one of the most underutilized methods of classing in JavaScript. It's not well understood compared to its older pseudoclassical brother. The irony is that pseudo-classical classing really employs prototypal classing behind the scenes. That's how flexible the prototypal inheritance pattern is, it can easily mimic a better known pattern. But, I'm getting ahead of myself (we'll discuss pseudoclassical next time).

Coming from C#, the Microsoft clone of Java, and deep diving into JavaScript has taught me a lot about inheritance. True classes are the blueprints used to create instances of those classes. The inheritance tree that results is not the same as the linked chain that the prototypal pattern follows.

Just as every instance of a leaf on a tree must belong to a specific set of branches all terminating in that one leaf, true classes must always fit into a hierarchy. Prototypal classes are really just objects in memory linked to other objects in memory. You can create an object and link it to a previously created object. It's purely a delegation relationship where if the subobject does not contain a property, it delegates the loopup for the property to its prototype delegate.

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

var subway = makeRestaurant('878 Main St.');
{% endraw %}
{% endhighlight %}

We could refactor this code to be a litte more effective by attaching the methodsObj directly to the a renamed makeRestaurant (now Restaurant) function.

{% highlight javascript %}
{% raw %}
var Restaurant = function(address) {...
  _.extend(instanceObj, Restaurant.methods);
...};
Restaurant.methods = {...};
{% endraw %}
{% endhighlight %}

This refactor gets us one step closer to prototypal classing. We're almost there, we just have a few changes to make.

{% highlight javascript %}
{% raw %}
var Restaurant = function(address) {
  var instanceObj = Object.create(Restaurant.methods); // Replaces _.extend call below

  instanceObj.address = address;
  instanceObj.isOpen = false;
  instanceObj.cashRegisters = 2;

  //_.extend(instanceObj, Restaurant.methods);  // Now we can remove this line

  return instanceObj;
}

Restaurant.methods = {
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

We changed the first line of the Restaurant function to use Object.create(). This establishes the delegate relationship to the methods object on the Restaurant itself. We can now remove the _.extend because it's unnecessary at this point. Now, when we call isOpen on a restaurant instance, it will fail on a local lookup, and succeed when it goes up the chain to the Restaurant.methods object.

This method confuses many, and is not used in the industry as often. Ironically, JavaScript was designed with this form of classing in mind.

Just one more tweak will get us to the final product:

{% highlight javascript %}
{% raw %}
var Restaurant = function(address) {
  var instanceObj = Object.create(Restaurant.methods); // Changed to Restaurant.prototype

  instanceObj.address = address;
  instanceObj.isOpen = false;
  instanceObj.cashRegisters = 2;

  return instanceObj;
}

Restaurant.prototype = { // Changed Restaurant.methods to Restaurant.prototype
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

This small change in the name of the object attached to the class is what trips people up the most about this method. Javascript gives you this object for free You don't need to declare it, it's just there everytime you create a function.  Prototype is an unfortunate name that has little direct meaning to what it does. It is really an object that contains functions.

Last, when we create our object to be returned in the `Restaurant` function, we must rename methods to prototype there as well. It should not read `Object.create(Restaurant.prototype)`. You cannot (definately should not), every try to change this relationship after a restaurant instance has been created. Some browsers let you much around with it, but you should abstain.

Many objects can all delegate to the same object, but the relationship is one way, and you are only allow to set the relationship to one object at creation. There's not overhead for declaring classes everywhere you need these relationships as in other languages, plus all these relationships are just references meaning no extra memory is needed when creating subclasses.

Speaking of subclasses, that's what I'll cover in the next series. Come back next week to learn about the pseudoclassical classing in JavaScript!

Feel free to contact me with an questions, or comment below: