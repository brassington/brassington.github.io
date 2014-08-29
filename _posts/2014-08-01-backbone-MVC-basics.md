---
layout: post
title: 'Backbone MVC Basics'
description: 'What is Backbone and how does it organize JS'
category: Javascript
tags: [Code, Javascript, Libraries]
image:
  feature: 
comments: true
share: true
---
Often, in today's world, customers and client expect high performing, single-page applications. Backbone is one tool that provides a structure necessary for creating these applications.

Backbone is a popular Javascript Application architecture library which provides classes and methods on the instanced objects which help you write more organized client-side applications.

### MVC

Backbone helps give your application an MVC architecture. This is a design pattern that keeps your code more modular through separation of concerns. It keeps your business data in Models away from your user interfaces in Views. A third piece, the Controller, coordinates the passing of information from one to the other.

The web uses the stateless HTTP protocal, meaning that the connection between a browser and any server does not stay open in between requests. Each request is a brand new information transaction.

It's very common to use a single, central handler to examine each HTTP request and send it to the correct controller to handle the request. This works well in the context of many requests and resulting page loads, but needs to be implemented differently when only one page is loaded initially on the client side.

### Backbone Models

In Backbone, Model instances are used to hold the data for each set of items in an application. The instances always extend the Backbone.Model, and simply defines defaults for values in the Model container.

{% highlight javascript %}
{% raw %}
var GroceryItem = Backbone.Model.extend({
  defaults: {
    name: '',
    bought: false
  }
});

var myGroceryItem = new GroceryItem({
  name: 'Milk'
});
{% endraw %}
{% endhighlight %}

Each model, in order to show up anywhere must be rendered on the page by a view.

### Backbone Views

Views extend the Backbone.View, must be instantiated with an associated Model. The `render()` method constructs the HTML for each item inside of an `li` element. So a View instance renders the new DOM elements based on the data contained in the attributes of a related model. 

Many views can use the same model, and models don't really have any direct way to know which views use them.

{% highlight javascript %}
{% raw %}
var GroceryView = Backbone.View.extend({
  tagName: 'li',
  gItemTpl: _.template( $(#item-template').html() ),
  events: {
    'dblclick label': 'edit',
    'keypress .edit': 'updateOnEnter',
    'blur .edit': 'close'
  },
  initialize: function() {
    this.$el = $('#todo');
  },
  render: function() {
    this.$el.html( this.gItemTpl( this.model.attributes ) );
    this.input = this.$('.edit');
    return this;
  },...

var myGroceryView = new GroceryView({model: myGroceryItem});
{% endraw %}
{% endhighlight %}

This code creates a model and corresponding view. There's plenty more to do to make this a functional Backbone application, but those are the basics of using Backone. Next post, we'll take a more detailed tour of a Backbone model.