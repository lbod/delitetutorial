# delite - creating custom components

## delite background
delite is a new JavaScript library born out of the [Dojo Toolkit Dijit framework](http://dojotoolkit.org/reference-guide/1.10/dijit).
This isn't a replacement per se but a repository to be used as the core building blocks for leveraging current and future standards
in HTML, CSS & JavaScript to build reusable Web Components.

It can be used on it's own but more likely used with other projects either from the [ibm-js repositories](https://github.com/ibm-js)
or others repositories.

More information can be found on the [delite website](http://ibm-js.github.io/delite/) explaining the standards this library aims to conform to.

## Tutorial details
**TODO maybe this could be multi-step, not sure how much should be in this tut yet**

In this tutorial you'll learn how to create your own custom elements, learn how to register them, learn how to use templates
& learn how you can bind data.

## Getting started
To quickly get started, we're using [https://github.com/ibm-js/generator-delite-element](https://github.com/ibm-js/generator-delite-element)
to install the required dependencies and create a basic **TODO** scaffold.
These steps are already explained but **TODO** we'll repeat that documentation using Yeoman to get started.

### create the scaffold
Install the `generator-delite-element` globally

    npm install -g generator-delite-element

And create a new directory (named my-element) and change directory to it

    mkdir -p my-element && cd $_

Run Yeoman to create our scaffold

    yo delite-element

You'll be prompted to enter the delite widget package name & the name of the custom widget element, accept the defaults for delite widget element package
& delite widget element name, select no for the rest of the options.

    [?] What is the name of your delite widget element package? my-element
    [?] What do you want to call your delite widget element (must contain a dash)? my-element-element
    [?] Would you like your delite element to be built on a template? No
    [?] Would you like your delite element to providing theming capabilities? No
    [?] Will your delite element require string internationalization? No
    [?] Will your delite element require pointer management? No

### A look through what's been generated

Lets look through what Yeoman created, again this is just a boilerplate setup but here's the important components:

- ./MyElementElement.js
- ./MyElementElement/css/MyElementElement.css
- ./samples/MyElementElement.html


Explain the components

## Lifecycle
Explain the main lifecycle methods

	this.preCreate();
	this.buildRendering();
	this.postCreate();

