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

**TODO: setup to be reviewed - this is WS project custom **

### create the scaffold

We'll let the `generator-delite-element` Yeoman generator do a basic setup for us which will quickly get us going.
(Start off with a simple custom element, no templating etc)

Install the `generator-delite-element` globally

    npm install -g generator-delite-element

And create a new directory (named first-delite-package, which will also be our package name) and change directory to it using the command :

    mkdir -p custom
    cd custom

Run Yeoman to create our scaffold

    yo delite-element

You'll be prompted to enter the widget package name & the name of the custom widget element, accept the defaults for delite widget element package
& delite widget element name, select no for the rest of the options.

    ? What is the name of your delite widget element package? (custom)
    ? What do you want to call your delite widget element (must contain a dash)? (custom-element)
    ? Would you like your delite element to be built on a template? n
    ? Would you like your delite element to providing theming capabilities? n
    ? Will your delite element require string internationalization? n
    ? Will your delite element require pointer management? n
    ? Do you want to use build version of delite package (instead of source version)? n

### A look through what's been generated
Lets look through what Yeoman created, again this is just a boilerplate setup but here's the important components.

We've created a new package named `custom` for new widgets that we'll create.

- `./CustomElement.js` - this is our widget module
- `./CustomElement/css/CustomElement.css` - this is our widget css
- `./samples/CustomElement.html` - this is a sample how to use our new widget

This is the most basic setup for a widget/custom component, you can view the sample generated HTML `./samples/CustomElement.html`
in a browser to see what's been created.
We'll build upon this example HTML as we progress in the tutorial.

    blurb about using dcl here? i.e. using requirejs we include dcl and delite/register which allows us to create a class
    via a module. It needs to be explained briefly but hint that you could do it inline in the page, not using a module.

Expand upon these resources & then add templating, then theming, internationalisation and pointer management

---

## Creating a custom element
Viewing the `./samples/CustomElement.html` example HTML we can see we've (partly) created the custom element declaratively in markup via

    <custom-element id="element" value="The Title"></custom-element>

For those who used the Dojo Toolkit Dijit framework previously, an important conceptual difference in delite is that the widget is the DOM node.
Dijit widgets instead, had a property which referenced the DOM node.

###Registering

The `<custom-element>` element doesn't constitute a custom element on it's own, it first needs to go through a registration process which is achieved using
the `delite/register` module. This is analogous to the HTML specification for registering custom elements
i.e. `document.registerElement('custom-element');`

If we look at the custom element module `./CustomElement.js` we see we register the custom element tag via:

```js

return register("custom-element", [HTMLElement, Widget], { .....

```

TODO: NOTE SURE WHAT THIS MEANS, THIS WAS FOR AN OLDER VERSION OF THE PARSER??:
(Note that here, we're not explicitly requiring the module for the custom element i.e. `"custom/CustomElement"`,
`delite/register` takes care of this for us for declarative created widgets).

This is an important concept which sometimes isn't clear at a first glance. You can add any non-standard tag to an HTML page and the HTML parser
will not complain, this is because these elements will be defined as a native
[`HTMLUnknownElement`](http://www.whatwg.org/specs/web-apps/current-work/multipage/dom.html#htmlunknownelement).
To create a custom element it must be **upgraded** first, this is what `delite/register` does. `delite/register` supports browsers who natively
support `document.registerElement` and those who don't.

The registration process above using `delite/register`, creates a custom element by registering the tag name `"custom-element"` as the first
argument and then inheriting (prototyping) the `HTMLElement` native element (as well as the `"delite/Widget"` module).

Elements which inherit from `HTMLElement`
using [valid custom element names](http://www.w3.org/TR/2013/WD-custom-elements-20130514/#dfn-custom-element-name) are custom elements.
The most basic requirement for the tag name is it **MUST** contain a dash **(-)**.

###Declarative creation of custom elements
If we view `./samples/CustomElement.html`, we see the following code:

```js
require(["delite/register", "custom/CustomElement"], function (register) {
    register.parse();
});
```

Declarative widgets (those created via markup in the page) need to be parsed in order to kick off the lifecycle of creating the widget.

###Programatic creation of custom elements
The generated example in `./samples/CustomElement.html` shows the declarative creation of custom elements, you can do the same thing
with programmatic creation

edit `./samples/CustomElement.html` i.e.

```js
require(["delite/register", "custom/CustomElement"], function (register) {
    register.parse();
});
```

to the following:

```js
require(["delite/register", "custom/CustomElement"], function (register, CustomElement) {
    register.parse();
    var anotherCustomElement = new CustomElement({value : 'another custom element title'});
    // note you must call startup() for programmatically created widgets
    anotherCustomElement.placeAt(document.body, 'last');
    anotherCustomElement.startup();
});
```

Note that programmatically created widgets should always call `startup()`. A helper function is provided for `delite/Widget` to place it
somewhere in the DOM named `placeAt`
(see the [documentation](https://github.com/ibm-js/delite/blob/master/docs/Widget.md#placement) for it's usage).
We need to also require the module for the custom element i.e. `"custom/CustomElement"` because we need to create a new
instance and then call it's methods.


The above would render: (default image width 460x242)


<img src='./images/programmatic_custom_element.gif'/>


**TODO: then go on to explain what prototyping `"delite/Widget"` does i.e. lifecycle methods**

###A look at the widget lifecycle methods for our simple widget
If we look at `"custom/CustomElement"` we can see two methods have been created for us, `render` and `refreshRendering`.
`render` is the most simplest of [lifecycle](https://github.com/ibm-js/delite/blob/master/docs/Widget.md#lifecycle)
methods we need to create our widget.

#### `render`
We normally wouldn't need to create a `render` method, typically we'd use templates to create our widget UI (which will be explained
later on) but because we aren't currently using a template we need to implement `render` to construct the widget UI for us. <br>
In our sample `render` method we're adding `<span>title</span>` and `<h1></h1>` elements to our widget as well as assigning a property
to the widget named `_h1` i.e. via `this.appendChild(this._h = this.ownerDocument.createElement("h1"));` which we can use to update
it programmatically or declaratively.


#### `refreshRendering`
`refreshRendering` is also a lifecycle method but implemented in `decor/Invalidating` which `delite/Widget` inherits from.
It's primary concern is to observe changes to properties defined on the widget and update the UI. In your web browser developer tools, if
you place a breakpoint in that method and then click the "click to change title" button, you'll see this method is called
(because the button adds inline JavaScript to update the element's value property).

If we wanted to see what the old value was (and also print it out to the DOM) we can change this method to the following:

```js
refreshRendering: function (props) {
    // if the value change update the display
    if ("value" in props) {
        this._h.innerText = "old= '" + props["value"] + "', new='" + this.value + "'";
    }
}
```

Notice when you first load the page, this method will be called for each widget & if you debug this method when you reload the page
you'll see that the `value` property of our widget is contained in the `props` argument. This is because we're setting the `value` property
on the declarative widget to `value="The Title"` and setting the value property on the programmatic widget to `value : "another custom element title"`.
If you don't set the `value` property of the widget at construction time, the `value` property of our widget is NOT contained in the `props` argument.

Click the 'click to change title button' which will render like:

<img src='./images/custom_element_old_new_props.gif'/>

If you still have a breakpoint set in `refreshRendering` you will see again that the `value` property of our widget is again contained in the `props`
argument.

Also, if you update the value `property` of `./CustomeElement.js` to:

```js
value: "The Title",
```

You'd notice again the `value` property of our widget is NOT contained in the `props` argument. This is because the property value hasn't changed.
The [decor/Invalidating](https://github.com/ibm-js/decor/blob/master/docs/Invalidating.md) documentation explains th

Lets undo this, change it back to

```js
value: "",
```

TODO: GOT TO HERE


###CSS
TODO: note this no longer works as per recent updates

If we look at the `./MyFirstElement.js` custom element module, we see there's a property defined named `baseClass` i.e. `baseClass: "my-first-element"`.
This adds a class name to the root node of our custom element (which you can see in the DOM using your debugger tools). Also notice we include
in the `define` the `delite/css!` plugin i.e. `"delite/css!./MyFirstElement/css/MyFirstElement.css"`. This plugin is obviously used to load CSS
for our custom element, it can also take a list of CSS files to load so lets see this in action.

Change the `define` of our custom element to the following:

```js
define([
    "delite/register",
    "delite/Widget",
    "decor/Invalidating",
    "delite/css!./MyFirstElement/css/MyFirstElement.css,./MyFirstElement/css/MyFirstElementSpan.css"
```

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
[Liaison](https://github.com/ibm-js/liaison). The handlebars implementation delite implements is primarily focused on performance).

We'll create a new delite custom element using Yeoman again.

In our `first-delite-package` folder run the `generator-delite-element` Yeoman generator with the following options (overwrite any files when
promoted):

    yo delite-element

    [?] What is the name of your delite widget element package? first-delite-package
    [?] What do you want to call your delite widget element (must contain a dash)? my-first-templated-element
    [?] Would you like your delite element to be built on a template? Yes
    [?] Would you like your delite element to providing theming capabilities? No
    [?] Will your delite element require string internationalization? No
    [?] Will your delite element require pointer management? No
       create MyFirstTemplatedElement/MyFirstTemplatedElement.html
       create MyFirstTemplatedElement.js
    identical package.json
     conflict bower.json
    [?] Overwrite bower.json? overwrite
        force bower.json
     conflict README.md
    [?] Overwrite README.md? overwrite
        force README.md
       create MyFirstTemplatedElement/css/MyFirstTemplatedElement.css
       create tests/MyFirstTemplatedElement.js
     conflict tests/intern.js
    [?] Overwrite tests/intern.js? overwrite
        force tests/intern.js
    identical tests/intern.local.js
    identical tests/intern.browser.js
    identical Gruntfile.js
       create samples/MyFirstTemplatedElement.html
    identical .jshintrc

This, as shown in the console output, creates:

- `./MyFirstTemplatedElement.js` - this is our widget module
- `./MyFirstTemplatedElement/css/MyFirstTemplatedElement.css` - this is our widget css
- `./MyFirstTemplatedElement/MyFirstTemplatedElement.html` - this is our widget template
- `./samples/MyFirstTemplatedElement.html` - this is a sample how to use our new widget

###Handlebars
If we look at the template we just created `./MyFirstTemplatedElement/MyFirstTemplatedElement.html` we can see it's created the following:

    <template>
        title:
        <h1>{{value}}</h1>
    </template>

All templates must be enclosed in a `<template>` element, we can see all the work we did in the `buildRendering` lifecycle method becomes much
more simple because now we're just dealing with HTML.

We don't need to implement the code in `buildRendering` of the non-templated example e.g. See our `./MyFirstTemplatedElement.js` widget module

```js
define([
    "delite/register",
    "delite/Widget",
    "delite/handlebars!./MyFirstTemplatedElement/MyFirstTemplatedElement.html",
    "delite/css!./MyFirstTemplatedElement/css/MyFirstTemplatedElement.css"
], function (register, Widget, template) {
    return register("my-first-templated-element", [HTMLElement, Widget], {
        baseClass: "my-first-templated-element",

        value: "",
        template: template
    });
});
```

All we need to do now is include the template using the handlebars plugin i.e.
`"delite/handlebars!./MyFirstTemplatedElement/MyFirstTemplatedElement.html"` and instead assign the resolved template to the `template` property
 of our widget i.e. `template: template`.

####Using handlebars templates
* im changing the title to an h2 (change css too), wrap in an `<article>` tag and add an object with properties (ive asked bill a question
about innerHTML of declarative elements and how to bind to widget/template properties)


What we've done so far isn't very exciting, so lets try and do something more 'real life'. Imagine we need to implement blogging widgets.
The widget needs to show the blog title (which we've already done with `{{value}}`, a date it was published, the author and the content of the
blog.

Let's make some changes:
#####Template
Change our template to add new properties for a blog author, when the blog was published and the text of the blog
in `./MyFirstTemplatedElement/MyFirstTemplatedElement.html`:

    <template>
        <article>
            <h3>{{value}}</h3>
            <p class='blogdetails'>Published at <span>{{articleDetails.publishDate}}</span> by <span>{{articleDetails.author}}</span></p>
            <p class='blog'>{{articleText}}</p>
        </article>
    </template>

`{{articleDetails.publishDate}}` and `{{articleDetails.author}}` are _path_ bindings to widget properties i.e. an object `articleDetails`
which has properties `publishDate` and `author`.


#####Template CSS
We've changed HTML in the template, adding new properties and classes, so change `./MyFirstTemplatedElement/css/MyFirstTemplatedElement.css` to:

    .my-first-templated-element {
        display: block;
    }

    .my-first-templated-element h3 {
        color: red;
    }

    .my-first-templated-element p.blogdetails span {
        font-weight: bold;
    }

    .my-first-templated-element p.blog {
        padding-left: 20px;
    }


#####Widget
Change our widget `./MyFirstTemplatedElement.js` to :

```js
define([
    "delite/register",
    "delite/Widget",
    "delite/handlebars!./MyFirstTemplatedElement/MyFirstTemplatedElement.html",
    "delite/css!./MyFirstTemplatedElement/css/MyFirstTemplatedElement.css"
], function (register, Widget, template) {
    return register("my-first-templated-element", [HTMLElement, Widget], {
        baseClass: "my-first-templated-element",

        value: "",
        template: template,
        articleDetails: {publishDate: 'foo', author: 'blah'},
        articleText : 'default article text'
    });
});
```

We've added an object property to our widget named `articleDetails` (which has `publishDate` and `author` properties) and a property named
`articleText`.

TODO: an explanation

#####Sample usage
(add in programmatic as well as declarative)

## topics
custom element
registering
templating (handlebars simple)

