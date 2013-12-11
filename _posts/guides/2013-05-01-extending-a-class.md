---
layout: guide
title: Extending a Class
published: true
category: guides
tags: [Getting Started, "@extends"]
comments: true
---

This guide will introduce you to some basic principles about inheritance in CocktailJS.

# Creating a Class
It is very simple to create classes with CocktailJS. Let's start with a very simple one. We want to 
create a `Person` class. The `Person` instance should be able to print a message in the console when
we ask it to `sayHi`.

Person.js

````javascript

var cocktail = require('cocktail'),
    Person = function(){};

cocktail.mix(Person, {

    sayHi: function(){
        console.log('Hi!');
    }
});

module.exports =  Person;

````

In the example above we just created a Person class with a method `sayHi()`. We can use our new
class to instantiate some objects:

index.js

````javascript

var Person = require('./Person'),
    joe;

joe = new Person();

joe.sayHi(); //will print "Hi!" in the console

````

# Extending a Class
Once we have created our `Person` we may want to add more abstractions to our model. Let's say we want to create 
a `Pirate` who is a `Person` and can `sayHi()` as well.

Pirate.js

````javascript

var cocktail = require('cocktail'),
    Person   = require('./Person'),
    Pirate   = function(){};

cocktail.mix(Pirate, {
    '@extends': Person
});

module.exports = Pirate;

````

The example shows how to extend from `Person` class. In CocktailJS everything is perform as a `mix`. In this case
we are telling the mix that we are extending from another class - Person - by passing the base class as a parameter
in the `@extends` annotation.
So, now `Pirate` is a `Person` and we can create some pirates objects:

index.js

````javascript

var Person = require('./Person'),
    Pirate = require('./Pirate'),
    joe, jack;

joe = new Person();

joe.sayHi(); //will print "Hi!" in the console

jack = new Pirate();

jack.sayHi(); //will print "Hi!" in the console

````

# Refactoring our classes
With the current abstraction we have no differences between `Person` and `Pirate`. Let's introduce some changes
about how each of them says "Hi".

Person.js

````javascript

var cocktail = require('cocktail'),
    Person = function(){};

cocktail.mix(Person, {
    
    greeting: 'Hello',

    sayHi: function(){
        console.log(this.greeting + "!");
    }
});

module.exports =  Person;

````

And now in `Pirate` we can override the `greeting` property with something more appropriate for a pirate:

Pirate.js

````javascript

var cocktail = require('cocktail'),
    Person   = require('./Person'),
    Pirate   = function(){};

cocktail.mix(Pirate, {
    '@extends': Person,

    greeting: 'Ahoy'

});

module.exports = Pirate;

````

Done! If we execute now our `index.js` file we will have a console message like this:

````console
$ node index.js
Hello!
Ahoy!
````
