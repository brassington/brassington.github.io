---
layout: post
title: 'Simple Tree Implementation'
description: 'Practice implementing a Tree data structure'
category: Javascript
tags: [Code]
image:
  feature: 
comments: true
share: true
---
It can be helpful to refresh some of the basics, and so this is a brief explanation of a tree data structure in Javascript.
I use the prototype style of inheritance to create an empty methods object, you could also just use the prototype object given with each object.
Then I create and return a new object linked to that methods object which has the defined methods for the tree.

index.js:
{% highlight javascript %}

var treeMaker = function (value) {
  var product = Object.create(treeMaker.methods);
  product.children = [];
  product.value = value || null;
  return product;
};

//methods go here!

treeMaker.methods = {};

treeMaker.methods.addChild = function(value) {
  var node = treeMaker(value);
  this.children.push(node);
  return node;
};

treeMaker.methods.contains = function(value) {
  var result = false;
  var subroutine = function(node) {
    node.children.forEach(function(element) {
      if (element.value === value) result = true;
      if (element.children.length > 0) subroutine(element);
    });
  };
  subroutine(this);
  return result;
};
{% endhighlight %}

Remember, with a tree the goal is for each node to have it's own children and the child nodes can also each have their own children, and so on and so forth. The DOM is really just a big instantiated tree structure.
So here are the tests we can now successfully run to create our tree and search for a value. Obviously, each node can have many more properties than a single value, just as there are many properties on every DOM node.

Using the implementation above:
{% highlight javascript %}
{% raw %}
var tree = treeMaker();
tree.addChild(1);
tree.addChild(2);
tree.contains(2);  --> 'true'
{% endraw %}
{% endhighlight %}

And that's a simple Tree implementation. You can check out a small repo for this Tree data structure here: [Simple Tree](https://github.com/jabbrass/simpleTree.js)

Find a better way I could have tackled this problem? Shoot me an email at andrew@brassington.io or leave a comment below:
