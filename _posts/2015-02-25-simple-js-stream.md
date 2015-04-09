---
layout: post
title: 'Simple JS Stream Class'
description: 'A simple implemenation of a stream in JS'
category: Javascript
tags: [Code]
image:
  feature: 
comments: true
share: true
---
A stream is an endless sequence of elements: somewhat like an unlimited array. Streams are useful for large sequences which need to be processed, and need to avoid dependency on fixed size.

A very basic stream implements two methods: head() and tail():
Head gets the first element of the current stream, while tail gets the rest of the current stream without the head.

For instance, you might use a stream to run an operation over the current element, and another operation to create the next element:
{% highlight javascript %}
var id = function(n) {return n};
var square = function(n) {return n * n};

var squaredSequence = new Stream(2, id, square);

console.log(squaredSequence.head()) // 2
console.log(squaredSequence.tail().head()) // 4
console.log(squaredSequence.tail().tail().head()) // 16
{% endhighlight %}

A simplified version like this allows you to create lazy sequences which don't calculate values until they're needed. By defining values in terms of functions which act on an initial element, we manufacture each element on demand.

So here's the simplified implementation:
{% highlight javascript %}
var Stream = function(start, output, stepping) {
  this.value = start;
  this.output = output;
  this.stepping = stepping;
};

Stream.prototype = {
  head: function() {
    return this.output(this.value);
  },
  tail: function() {
    return new Stream(this.stepping(this.value), this.output, this.stepping);
  },
};
{% endhighlight %}

Streams will often have additional methods such as nth(), which executes .tail() n times and returns the head(). Map() and Take() are also common. Try implementing the additional methods, and shoot me an email if you run into trouble at andrew@brassington.io or leave a comment below:
