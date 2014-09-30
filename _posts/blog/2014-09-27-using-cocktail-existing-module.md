---
layout: post
title: Using Cocktail In Your Current Project
category: blog
tags: [javascript, oop, traits]
published: true
comments: true
---

CocktailJS is a very versatile library. You can follow the single argument class definition where you can define your properties and methods, extend from another class, mix traits and export the class all in one single operation. But sometimes you already have a module definition where you want to keep the current code style. In this post we are going to explore how to use Traits or Talents in an exisiting class definition.

<!--break-->

To use a trait in an already defined module is quite easy. We can mix the trait right into the class or we can do it inside the constructor as a talent.

> Surface.js

````javascript

module.exports = Surface;

function Surface(x, y, height, width) {
    this.x = x;
    this.y = y;
    this.height = height;
    this.width = width;
}

Surface.prototype.setPosition = function (position) {
    this.x = position.x;
    this.y = position.y;
}

Surface.prototype.setSize = function (size) {
    this.height = size.height;
    this.width = size.width;
}

// more surface methods ...

````

This very simple example is enough to demonstrate how to use a simple trait. In the Surface constructor we receive four parameters, two of them define the position, and the other two the size. Then we have a setter for the size which gets an object defining the size in terms of height and width, and another setter for the position defined by an object containing the x and y coordinates.

To easily refactor this class, and to showcase how to use a trait in this example, we are going to use the [Configurable](/extensions/traits-configurable.html) trait.

## Adding the Configurable Trait 

As it was mentioned before, we can mix a trait right into the class definition. The `mix` method accepts one or two parameters. We can mix our trait definition right into the Surface class:

> Surface.js 

````javascript

var cocktail = require('cocktail'), // #1 
    Configurable = require('cocktail-trait-configurable'); // #2

module.exports = Surface;

function Surface(x, y, height, width) {
    // #4
    this.configure({
        position: {x: x, y: y},
        size: {height: height, width: width}
    });
}

// #3
cocktail.mix(Surface, {'@traits': [Configurable]});

Surface.prototype.setPosition = function (position) {
    this.x = position.x;
    this.y = position.y;
}

Surface.prototype.setSize = function (size) {
    this.height = size.height;
    this.width = size.width;
}

// more surface methods ...

````

We have added our dependencies in \#1 and \#2: cocktail and a reference to our Configurable trait that adds the `configure` method to the host class, in this case the `Surface` class.

Then we mix, or apply the Configurable trait to the Surface class as it is shown in \#3.

Finally, we did a small refactor to our constructor to use the configure method we just added to take care of our parameters. Since we have two setters one for position and one for size, then we can pass an object containing those properties to the configure method as it is done in \#4.

## Using Configurable as a Talent

We have said that a Trait can be used a Talent when we want to add more functionality to a given instance. Taking the same Surface example, we can demonstrate this easily using Configurable as a Talent right in our Surface constructor:

> Surface.js 

````javascript

var cocktail = require('cocktail'), // #1 
    Configurable = require('cocktail-trait-configurable'); // #2

module.exports = Surface;

function Surface(x, y, height, width) {

    // #3
    cocktail.mix(this, {'@talents': [Configurable]});

    // #4
    this.configure({
        position: {x: x, y: y},
        size: {height: height, width: width}
    });
}


Surface.prototype.setPosition = function (position) {
    this.x = position.x;
    this.y = position.y;
}

Surface.prototype.setSize = function (size) {
    this.height = size.height;
    this.width = size.width;
}

// more surface methods ...

````

The \#1, \#2 and \#4 are the same as in the Trait implementation. But \#3 is a little different. Since inside our contructor `this` is the current instance we are creating, we can agument it using `Configurable` as a Talent.

