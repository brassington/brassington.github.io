---
layout: post
title: Creating a Famo.us Textarea Surface
description: 
category: Famous
tags: [Famous, Code, Javascript, Github]
image:
  feature: 
comments: true
share: true
---

Famo.us is all Javascript, and it uniquely places all DOM elements in a flat structure using absolute positioning to manage where the elements should show on the page. For every html element you need to display, you need a surface to render it. Now you can add as many elements to the content portion of a surface, but if you want it to independently interact with other surfaces using custom methods, you may need to write your own surface.

I'd been lurking on the Famo.us IRC channel, and happened to catch another developer who was having problems creating a text area in Famous. There was no surface that would create a textarea element, and so I took it upon myself to figure out how difficult it would be to create one. It turns out, it wasn't that hard at all. 

### Creating a Custom Surface

First, you'll need to initialize your new surface by using define from require.js to bring in the core surface object you'll be extending to create your own surface.

{% highlight javascript %}
{% raw %}
define(function(require, exports, module) {
    var Surface = require('famous/core/Surface');
{% endraw %}
{% endhighlight %}

Now you must set all the options you'll need to take in as arguments from anyone who want to reuse your surface. It's also not a bad idea to add documentation taking particular note to explain how each of your parameter's should be used.

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