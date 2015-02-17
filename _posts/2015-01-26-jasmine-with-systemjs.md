---
layout: post
title: 'Jasmine Unit Testing with SystemJS'
description: 'Writing unit tests using SystemJS + Jasmine'
category: Javascript
tags: [Code]
image:
  feature: 
comments: true
share: true
---
I've continued to use System.js, and struggled with bootrapping unit tests in the module loader. One of the benefits of using such a module loader is that each module gets loaded into its own protective function.
This keeps all the variables private and away from the global scope unless exposed purposefully. One challange with this approach is testibility with Jasmine.
Jasmine, by default, looks for global objects available by loading in a spec file and the javascript you'd like to test.

We can use Jasmine to unit test the modules loaded by System.js first by registering the Jasmine files in the dependecy cache in a settings file:

settings.js:
{% highlight javascript %}
var baseURL = '', base = baseURL;
var paths = {
        // Unit tests
        'jasmine' : base+'lib/jasmine/lib/jasmine-core/jasmine.js',
        'jasmine-html' : base+'lib/jasmine/lib/jasmine-core/jasmine-html.js',
        'jasmine-boot' : base+'lib/jasmine/lib/jasmine-core/boot.js'
    };

    System.config({
        baseURL: baseURL,
        paths: paths
    });
{% endhighlight %}

I've also set the baseURL for this project, because it doesn't exist on the root directory when in production. System.js has to know what directory to look for each of the dependecies relative to the root of the webhost.
Inside of the same function expression, I also add the following code to mimic Require.js ability to locate and load a default file as an attribute on the script tag which loads the library.

Using the implementation above:
{% highlight javascript %}
{% raw %}
if ((document.querySelector('[data-main]')) && (mainjs = document.querySelector('[data-main]').getAttribute('data-main'))) {
    var fileName = mainjs.replace('.js', '');
    var moduleName = fileName.split('/').join('-');
    (paths[moduleName] !== undefined) ? System.import(moduleName) : System.import(fileName);
}
{% endraw %}
{% endhighlight %}

Next, we need to add the reference to SpecRunner.html. By default this file will run any scripts included with script tags along with their corresponding spec.js files.

{% highlight html %}
{% raw %}
  <script data-main="js/main.js" src="../lib/system.js/dist/system.js"></script>
  <script src="/js/config.js"></script>
{% endraw %}
{% endhighlight %}

Now you can start including files using System.js, including any spec files you'd like to run.

{% highlight html %}
{% raw %}
  <!-- include spec files here... -->
  <script>System.import("/js/spec/list-spec")</script>
{% endraw %}
{% endhighlight %}

Last, here's a quick example of what kind of the type of easy CommonJS syntax you can use to load up any code you'd like to test:

{% highlight html %}
{% raw %}
equire('jasmine');
require('jasmine-html');
require('jasmine-boot');

var $ = require('jquery');
var Handlebars = require('handlebars').default;
var ajax = require('ajax');
var get = ajax.get, post = ajax.post, handleError = ajax.handleError;

describe("List", function() {

  beforeEach(function() {
  });

  it("jQuery should be defined", function() {
    expect($).toBeDefined();
  });

  it("Handlebars should be defined", function() {
    expect(Handlebars).toBeDefined();
  });
});

window.onload();
{% endraw %}
{% endhighlight %}

It took me a while to figure out that adding `window.onload();` at the end of the file while actually rebootstrap Jasmine and allow it to work with files which are required in with System.js.
The tradeoff here is that when using System.js, you need to keep your code modular enough that you can load in and test the modules using Jasmine.

Find a better way I could have tackled this problem? Shoot me an email at andrew@brassington.io or leave a comment below:
