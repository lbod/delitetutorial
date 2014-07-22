# delite - creating custom components

## delite background
delite is a new JavaScript library born out of the [Dojo Toolkit Dijit framework](http://dojotoolkit.org/reference-guide/1.10/dijit).
This isn't a replacement per se but a repository to be used as the core building blocks for leveraging current and future standards
in HTML, CSS & JavaScript to build reusable Web Components.

It can be used on it's own but more likely used with other projects either from the [ibm-js repositories](https://github.com/ibm-js)
or other repositories.

More information can be found on the [delite website](http://ibm-js.github.io/delite/) explaining the standards this library aims to conform to.

## Tutorial details
**TODO maybe this could be multi-step, not sure how much should be in this tut yet**

In this tutorial you'll learn how to create your own custom elements, learn how to register them, learn how to use templates
& learn how you can bind data. It's a beginner tutorial so we won't be delving too deep into what delite provides (yet!!!)

## Getting started
To quickly get started, we're using [https://github.com/ibm-js/generator-delite-element](https://github.com/ibm-js/generator-delite-element)
to install the required dependencies and create a basic scaffold.
These steps are already explained but we'll repeat that documentation using Yeoman to get started.

---

**TODO: setup to be reviewed**

### create the scaffold

We'll let the `generator-delite-element` Yeoman generator do a basic setup for us which will quickly get us going.
(Start off with a simple custom element, no templating etc)

Install the `generator-delite-element` globally

    npm install -g generator-delite-element

And create a new directory (named first-delite-package, which will also be our package name) and change directory to it using the command :

    mkdir -p first-delite-package  && cd $_

Run Yeoman to create our scaffold

    yo delite-element

You'll be prompted to enter the widget package name & the name of the custom widget element, accept the defaults for delite widget element package
& delite widget element name, select no for the rest of the options.

    [?] What is the name of your delite widget element package? first-delite-package
    [?] What do you want to call your delite widget element (must contain a dash)? my-first-element
    [?] Would you like your delite element to be built on a template? N
    [?] Would you like your delite element to providing theming capabilities? N
    [?] Will your delite element require string internationalization? N
    [?] Will your delite element require pointer management? N

### A look through what's been generated
Lets look through what Yeoman created, again this is just a boilerplate setup but here's the important components.

We've created a new package named `first-delite-package` for new custom elements that we'll create.

- `./MyFirstElement.js` - this is our custom element class
- `./MyFirstElement/css/MyFirstElement.css` - this is our custom element css
- `./samples/MyFirstElement.html` - this is a sample how to use our new custom element

This is the most basic setup for a custom component, you can view the sample generated HTML `./samples/MyFirstElement.html`
in a browser to see what's been created.
We'll build upon this example HTML as we progress in the tutorial.

    blurb about using dcl here? i.e. using requirejs we include dcl and delite/register which allows us to create a class
    via a module. It needs to be explained briefly but hint that you could do it inline in the page, not using a module.

Expand upon these resources & then add templating, then theming, internationalisation and pointer management

---

## Creating a custom element
Viewing the `./samples/MyFirstElement.html` example HTML we can see we've (partly) created the custom element declaratively in markup via

    <my-first-element id="element" value="The Title"></my-first-element>

For those who used the Dojo Toolkit Dijit framework previously, an important conceptual difference in delite is that the widget is the DOM node.
Dijit widgets instead, had a property which referenced the DOM node.

###Registering

`<my-first-element/>` doesn't constitute a custom element on it's own, it first needs to go through a registration process which is achieved using
the `delite/register` module. This is analogous to the HTML specification for registering custom elements
i.e. `document.registerElement('my-first-element');`

If we look at the custom element module `./MyFirstElement.js` we see we register the custom element tag via:

    return register("my-first-element", [HTMLElement, Widget, Invalidating], { .....
(Note that here, we're not explicitly requiring the module for the custom element i.e. `"first-delite-package/MyFirstElement"`,
`delite/register` takes care of this for us for declarative created widgets).

This is an important concept which sometimes isn't clear at a first glance. You can add any non-standard tag to an HTML page and the HTML parser
will not complain, this is because these elements will be defined as a native
[`HTMLUnknownElement`](http://www.whatwg.org/specs/web-apps/current-work/multipage/dom.html#htmlunknownelement).
To create a custom element it must be **upgraded** first, this is what `delite/register` does. `delite/register` supports browsers who natively
support `document.registerElement` and those who don't.

The registration process above using `delite/register`, creates a custom element by registering the tag name `"my-first-element"` as the first
argument and then inheriting (prototyping) the `HTMLElement` native element (as well as `"delite/Widget"` and `"decor/Invalidating"`modules).

Elements which inherit from `HTMLElement`
using [valid custom element names](http://www.w3.org/TR/2013/WD-custom-elements-20130514/#dfn-custom-element-name) are custom elements.
The most basic requirement for the tag name is it **MUST** contain a dash **(-)**.

###Declarative creation of custom elements
If we view `./samples/MyFirstElement.html`, we see the following code:

    require("delite/register", function (register) {
    	register.parse();
    });
Declarative widgets (those created via markup in the page) need to be parsed in order to kick off the lifecycle of creating the widget.

###Programatic creation of custom elements
The generated example in `./samples/MyFirstElement.html` shows the declarative creation of custom elements, you can do the same thing
with programmatic creation

edit `./samples/MyFirstElement.html` i.e.

    require("delite/register", function (register) {
    	register.parse();
    });
to the following:

    require(["delite/register", "first-delite-package/MyFirstElement"], function (register, MyFirstElement) {
        register.parse();
        var anotherCustomElement = new MyFirstElement({value : 'another custom element title'});
        // note you must call startup() for programmatically created widgets
        anotherCustomElement.startup();
        anotherCustomElement.placeAt(document.body, 'last');
    });

Note that programmatically created widgets should always call `startup()`. A helper function is provided for `delite/Widget` to place it
somewhere in the DOM named `placeAt`
(see the [documentation](https://github.com/ibm-js/delite/blob/master/docs/Widget.md#placement) for it's usage).
We need to also require the module for the custom element i.e. `"first-delite-package/MyFirstElement"` because we need to create a new
instance and then call it's methods.


The above would render: (default image width 460x242)


<img src='./images/programmatic_custom_element.gif'/>


**TODO: then go on to explain what prototyping `"delite/Widget"` does i.e. lifecycle methods**

###A look at the widget lifecycle methods for our simple widget
If we look at `"first-delite-package/MyFirstElement"` we can see two methods have been created for us, `buildRendering` and `refreshRendering`.
`buildRendering` is the most simplest of [lifecycle](https://github.com/ibm-js/delite/blob/master/docs/Widget.md#lifecycle)
methods we need to create our widget.

#### `buildRendering`
We normally wouldn't need to create a `buildRendering` method, typically we'd use templates to create our widget UI (which will be explained
later on) but because we aren't currently using a template we need to implement `buildRendering` to construct the widget UI for us. <br>
In our sample `buildRendering` method we're adding `<span>title</span>` and `<h1></h1>` elements to our widget (as well as assigning a property
to the widget named `_h1` i.e. via `this.appendChild(this._h = this.ownerDocument.createElement("h1"));`.


#### `refreshRendering`
`refreshRendering` is also a lifecycle method but implemented in `decor/Invalidating` which `delite/Widget` inherits from.
It's primary concern is to observe changes to properties defined on the widget and update the UI. In your web browser developer tools, if
you place a breakpoint in that method and then click the "click to change title" button, you'll see this method is called
(because the button adds inline JavaScript to update the element's value property).

If we wanted to see what the old value was (and also print it out to the DOM) we can change this method to the following:

    refreshRendering: function (props) {
        // if the value change update the display
        if ("value" in props) {
            this._h.innerText = "old= '" + props["value"] + "', new='" + this.value + "'";
        }
    }

Notice when you first load the page, this method will be called for each widget, this is because we're setting the `value` property on the
declarative widget to `value="The Title"` and setting the value property on the programmatic widget to `value : "another custom element title"`.

Click the 'click to change title button' which will render like:

<img src='./images/custom_element_old_new_props.gif'/>

**TODO** : (review this, maybe not needed but it is weird behaviour, maybe some direction here to set an initial default value? e.g. angular
has the ngBindTemplate directive)
If you updated the value `property` of `./MyFirstElement.js` to:

    value: "The Title",

You'd notice that the `if ("value" in props) {` condition isn't true for the declarative custom element we added. This is because the property
value hasn't changed.

Lets undo this, change it back to

    value: "",
**END TODO**

###CSS
If we look at the `./MyFirstElement.js` custom element module, we see there's a property defined named `baseClass` i.e. `baseClass: "my-first-element"`.
This adds a class name to the root node of our custom element (which you can see in the DOM using your debugger tools). Also notice we include
in the `define` the `delite/css!` plugin i.e. `"delite/css!./MyFirstElement/css/MyFirstElement.css"`. This plugin is obviously used to load CSS
for our custom element, it can also take a list of CSS files to load so lets see this in action.

Change the `define` of our custom element to the following:

    define([
        "delite/register",
        "delite/Widget",
        "decor/Invalidating",
        "delite/css!./MyFirstElement/css/MyFirstElement.css,./MyFirstElement/css/MyFirstElementSpan.css"

Then create this new CSS file at `./MyFirstElement/css/MyFirstElementSpan.css` with

    .my-first-element span {
        color: blue;
    }
Reload the page and you'll see the new CSS file being loaded in your development tools and the new style being applied.

Obviously this is an unrealistic example but shows how the `delite/css!` plugin can be used. Later on, we'll show how to use the theming capabilities.


## Lifecycle methods for our simple widget (expand on later when using template or do this here?)
Explain the main lifecycle methods

	this.preCreate();
	this.buildRendering();
	this.postCreate();

##Templates
What we've done so far is obviously a very rudimentary demonstration. We wouldn't expect to programmatically create DOM nodes & this is where
delite comes into it's own. Out of the box, delite supports templates using an in built implementation of [Handlebars](http://handlebarsjs.com/).
We won't need to programmatically create DOM nodes in `buildRendering`, creating a template will do all the work for us.

(Note there are some limitations using the `delite/handlebars!` plugin in delite, namely it doesn't support iterators or TODO: conditionals,
however in most cases this isn't a limiting factor. Support for this will be explained in a later more advanced tutorial when we discuss
[Liaison](https://github.com/ibm-js/liaison)).



## topics
custom element
registering
templating (handlebars simple)

