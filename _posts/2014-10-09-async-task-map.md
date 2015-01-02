---
layout: post
title: 'Asyncronous Task Map'
description: 'How to keep track of asyncronous functions, and run a function on all their results'
category: Javascript
tags: [Code]
image:
  feature: 
comments: true
share: true
---
Playing with JavaScript is often one of the best ways to learn. Just pop open that console in your browser and start playing!

One problem that I really enjoyed solving was one where I needed to keep track of list of functions, invoke them all, and modify their results. I call this an asyncronous task mapping function.
The tricky part is waiting until each of the functions has resolved before modifything the results.

The asyncTaskMap will take an array of functions as the first arguement, each function will take a callback that we can generate, and invoke that callback with information, similar to a JSONP response. The second arguement will be a function which will be invoked on all the results in the order they were given.

First, we want to create an array which will store the results from each of the list of functions. I'm using the `new` keyword instead of the literal notation to be more explicit about the fact that it will contain many undefined (empty) values until they're backfilled when each response is returned asyncronously.
Next, we'll initialize a variable to keep track of how many items have already asyncronously returned and how many items are been places in the results array. We must do this because if you try the following:

{% highlight javascript %}
var results = [];
results[9] = 'First';

results.length // -> 10, but there's only on item in the array
{% endhighlight %}

You'll notice that when you insert items out of order in an array, the length will not reflect the number of items in the array. This can be called a `sparse` arary meaning not all the positions have values defined.
We need to keep track of how many functions have returned so that when they're all returned, we can trigger mapping the callback onto each of the results.

Next, we'll iterate through each of the task functions and generate a corresponding function which can be fed as a callback to each of our tasks. This is what the `cbMap` is doing: keeping track of the order of the functions so they can be looked up when invoking each task.
Each function generated stores the results in the results array, in the same order as the task array. We much check after each function has returned wheather all the tasks have returned yet, thus the test comparing `task`s size to `result`s size.

Last, we iterator over each task, and invoke it with the function generated for it in the cbMap.

{% highlight javascript %}
{% raw %}
var asyncTaskMap = function(tasks, callback){
  var results = new Array(tasks.length);
  var resultSize = 0;
  var cbMap = {};

  if (Array.isArray(tasks) !== true) { throw "Tasks is not an array!"; };
  if (typeof callback !== 'function') { throw "Callback is not a function"; }

  tasks.forEach( function(item, iterator) {
    var num = iterator;
    cbMap[num] = function(value) {
      results[num] = value;
      resultSize++;
      if ( resultSize === tasks.length ) {
        results.forEach(function(item){
          callback(item);
        })
      }
    }
  });

  tasks.forEach( function(task, iterator) {
    task(cbMap[iterator]);
  });

};
{% endraw %}
{% endhighlight %}

Have any questions? Find a better way I could have tackled this problem? Shoot me an email at andrew@brassington.io or leave a comment below:
