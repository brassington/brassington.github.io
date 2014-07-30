---
layout: post
title: A Tour of Inheritance Patterns in Javascript
description: 
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

This is a class in its simplest form. It creates objects, as many as desired. One problem with this method is that each function attached to the object needs it's own memory. We can optimize this slightly by defining a function outside the maker-function, and adding a reference on the class to the other named function. This way, we only have a reference to the single function defined in one place and occupying a place in memory once:

{% highlight javascript %}
{% raw %}
var makeRestaurant = function(address) {
  var instanceObj = {};

  instanceObj.address = address;
  instanceObj.isOpen = false;
  instanceObj.cashRegisters = 2;
  
  instanceObj.open = open; // <- This is the reference to the open function defined below.

  return instanceObj;
}

var open = function() {
  this.isOpen = true;
}

{% endraw %}
{% endhighlight %}

{% highlight javascript %}
    /**
     * A Famo.us surface in the form of an HTML textarea element.
     *   This extends the Surface class.
     *
     * @class TextareaSurface
     * @extends Surface
     * @constructor
     * @param {Object} [options] overrides of default options
     * @param {string} [options.placeholder] placeholder text hint that describes
     *  the expected value of an <textarea> element
     * @param {string} [options.value] value of text
     * @param {string} [options.name] specifies the name of textarea
     * @param {string} [options.wrap] specify 'hard' or 'soft' wrap for textarea
     * @param {number} [options.cols] number of columns in textarea
     * @param {number} [options.rows] number of rows in textarea
     */
    function TextareaSurface(options) {
        this._placeholder = options.placeholder || '';
        this._value       = options.value || '';
        this._name        = options.name || '';
        this._wrap        = options.wrap || '';
        this._cols        = options.cols || '';
        this._rows        = options.rows || '';

        Surface.apply(this, arguments);
        this.on('click', this.focus.bind(this));
    }

{% endhighlight %}

In the TextareaSurface function, I've defined the list of default values if someone doesn't define use an argument. This is an important practice, and though I've left them all blank, it would be easy to come back and set some other values as default.

Next, use Object.create() to specify the core Surface object that you want to link as the prototype. This will help to extend the Surface object, allowing it to be more useful by giving Javascript another object to examine for similar properties and methods. We are creating a specialized surface after all:
{% highlight javascript %}
    TextareaSurface.prototype = Object.create(Surface.prototype);
    TextareaSurface.prototype.constructor = TextareaSurface;

    TextareaSurface.prototype.elementType = 'textarea';
    TextareaSurface.prototype.elementClass = 'famous-surface';
{% endhighlight %}

Now, if someone doesn't send in an entire object to create our new kind of Surface, we want to include chainable extension methods that make it possible to easily modify the surface once it's been created in memory. This is what the majority of the file's contents: methods which will set values in place of the defaults defined at the top of the file.

Again, it's wise to document the usage of the extension method and whether it triggers a repaint or not. In this case, the method can be overridden by another property, the size property, so I've noted as much and included an example:
{% highlight javascript %}
    /**
     * Set the number of columns visible in the textarea.
     *   Note: Overridden by surface size; set width to true. (eg. size: [true, *])
     *         Triggers a repaint next tick.
     *
     * @method setColumns
     * @param {number} num columns in textarea surface
     * @return {TextareaSurface} this, allowing method chaining.
     */
    TextareaSurface.prototype.setColumns = function setColumns(num) {
        this._cols = num;
        this._contentDirty = true;
        return this;
    };
{% endhighlight %}

The last step is to place the element into the document as a new node. Famous calls this deploying the component. It could be a good practice to include some tests for blank values and avoid changing the element if the values were never set. This is more efficient and helps avoid empty attributes on elements.

{% highlight javascript %}
    /**
     * Place the document element this component manages into the document.
     *
     * @private
     * @method deploy
     * @param {Node} target document parent of this container
     */
    TextareaSurface.prototype.deploy = function deploy(target) {
        if (this._placeholder !== '') target.placeholder = this._placeholder;
        if (this._value !== '') target.value = this._value;
        if (this._name !== '') target.name = this._name;
        if (this._wrap !== '') target.wrap = this._wrap;
        if (this._cols !== '') target.cols = this._cols;
        if (this._rows !== '') target.rows = this._rows;
    };

    module.exports = TextareaSurface;
});
{% endhighlight %}
The last line exports the new surface as a new module for Require.js to use, and for you to include in any of the rest of your Famous app.

You will really learn a ton by jumping into the code and figuring Famous out, reading it line by line. I certainly have learned plenty. I've posted this new surface in a forked version of the main Famous/Surface repository here: [TextareaSurface.js](https://github.com/jabbrass/surfaces/blob/d9d6838ba34f2012d632eac619d009d80aa329c5/TextareaSurface.js).
Feel free to check it out and play with it for yourself!

{% highlight javascript %}
<html>
<body>
<script src="../lib/underscore/underscore.js"></script>

  <script>
    var stacksFunc = [];
    var stacksfuncShared = [];
    var stacksProto = [];
    var stacksPseudo = [];

    var makeStackA = function(){
      var someInstance = {};


  var storage = {};
  var size = 0;

  someInstance.push = function(value){
    storage[size] = value;
    size++;
  };

  someInstance.pop = function(){
    if (size > 0) {
      size--;
      var temp = storage[size];
      delete storage[size];
      return temp;
    }
  };

  someInstance.size = function(){
    return size;
  };

  return someInstance;
};

var makeStackB = function() {
  var result = {};
  _.extend(result, stackMethods, {'storage' : {'size' : 0}});
  return result;
};

var stackMethods = {
  'push' : function(value){
    var storage = this.storage;
    storage[storage.size] = value;
    storage.size++;
  },
  'pop' : function(){
    var storage = this.storage;
    if (storage.size > 0) {
      storage.size--;
      var temp = storage[storage.size];
      delete storage[storage.size];
      return temp;
    }
  },
  'size' : function(){
    return this.storage.size;
  }
};

var makeStackC = function() {
  var result = Object.create(stackMethods);
  result.storage = {'size':0};
  return result;
};

var Stack = function() {
  this.storage = {size: 0};
};

Stack.prototype.push = function(value){
  var storage = this.storage;
  storage[storage.size] = value;
  storage.size++;
};

Stack.prototype.pop = function(){
  var storage = this.storage;
  if (storage.size > 0) {
    storage.size--;
    var temp = storage[storage.size];
    delete storage[storage.size];
    return temp;
  }
};

Stack.prototype.size = function(){
  return this.storage.size;
};


for (var i = 0; i < 100000; i++) {
  stacksFunc[i] = makeStackA();
  stacksfuncShared[i] = makeStackB();
  stacksProto[i] = makeStackC();
  stacksPseudo[i] = new Stack();
}




</script>
</body>
</html>
{% endhighlight %}
