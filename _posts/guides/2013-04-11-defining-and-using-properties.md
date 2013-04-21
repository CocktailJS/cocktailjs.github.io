---
layout: guide
title: Defining & Using Properties
published: true
category: guides
tags: [Getting Started]
---

This guide is intended to explain the usage of the `@properties` annotation. Defining and using properties not only 
while you define a class but also as a handy mechanism to add getters and setters even in objects.

# Creating a Class with CocktailJS

To create a class in CocktailJS from scratch, we need to define a class constructor variable that will be used
as a subject into the mix. The very basic structure looks like this:

MyClass.js

    var Cocktail = require('Cocktail'),
        MyClass = function(){};

    Cocktail.mix(MyClass, {

    });

    module.exports = MyClass;

Here we have created and exported our class `MyClass`. Right now there is nothing done in the mix since the option
object is just an empty object.
Let's start adding some behavior to our class. In the following example we have added a simple method to display a
message in the console which we called `sayHello`:

MyClass.js

    var Cocktail = require('Cocktail'),
        MyClass = function(){};

    Cocktail.mix(MyClass, {

        sayHello: function(){
            console.log('Hello Cocktail!');
        }

    });

    module.exports = MyClass;

# Defining Properties

CocktailJS has a handy annotation `@properties` which helps you to define properties and their getters and setter.
We are going to add a property named `greeting` into our `MyClass` so we can change the message on `sayHello` method.

First, we have to define the `greeting` property:

    '@properties' : {
        greeting: 'Hello'
    }

And then change the `sayHello` method to use the getter.

    sayHello: function(){
        var greeting = this.getGreeting();

        console.log(greeting + ' Cocktail!');
    }

All together:

MyClass.js

    var Cocktail = require('Cocktail'),
        MyClass = function(){};

    Cocktail.mix(MyClass, {

        '@properties' : {
            greeting: 'Hello'
        },

        sayHello: function(){
            var greeting = this.getGreeting();

            console.log(greeting + ' Cocktail!');
        }

    });

    module.exports = MyClass;

Now we can use MyClass to produce different messages:

index.js

    var MyClass = require('./MyClass'),
        obj;

    obj = new MyClass();

    obj.sayHello(); // prints "Hello Cocktail!" since the default value is Hello

    obj.setGreeting('Ahoy');
    obj.sayHello(); // prints "Ahoy Cocktail!"

    obj.setGreeting('Yo');
    obj.sayHello(); // prints "Yo Cocktail!"





