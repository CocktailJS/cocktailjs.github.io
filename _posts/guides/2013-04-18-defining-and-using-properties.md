---
layout: guide
title: Defining & Using Properties
published: true
category: guides
tags: [Getting Started, "@properties"]
comments: true
---

This guide is intended to explain the `@properties` annotation through some simple examples.

# Creating a Class with cocktailJS

To create a class in cocktailJS from scratch, we need to define a class constructor variable that will be used
as a subject into the mix. The very basic structure looks like this:

MyClass.js

````javascript
var cocktail = require('cocktail'),
    MyClass = function(){};

cocktail.mix(MyClass, {
    // ....
});

module.exports = MyClass;
````

Here we have created and exported our class `MyClass`. Right now there is nothing done in the mix since the option
object is just an empty object.
Let's start adding some behavior to our class. In the following example we have added a simple method to display a
message in the console which we called `sayHello`:

MyClass.js

````javascript

var cocktail = require('cocktail'),
    MyClass = function(){};

cocktail.mix(MyClass, {

    sayHello: function(){
        console.log('Hello cocktail!');
    }

});

module.exports = MyClass;

````

# Defining Properties

cocktailJS has a handy annotation `@properties` which helps you to define properties and their getters and setter.
We are going to add a property named `greeting` into our `MyClass` so we can change the message on `sayHello` method.

First, we have to define the `greeting` property:

````javascript

'@properties' : {
    greeting: 'Hello'
}

````

And then change the `sayHello` method to use the getter.

````javascript

sayHello: function(){
    var greeting = this.getGreeting();

    console.log(greeting + ' cocktail!');
}

````

MyClass class will look like the following:

MyClass.js

````javascript

var cocktail = require('cocktail'),
    MyClass = function(){};

cocktail.mix(MyClass, {

    '@properties' : {
        greeting: 'Hello'
    },

    sayHello: function(){
        var greeting = this.getGreeting();

        console.log(greeting + ' cocktail!');
    }

});

module.exports = MyClass;

````

Now we can use MyClass to produce different messages:

index.js

````javascript

var MyClass = require('./MyClass'),
    obj;

obj = new MyClass();

obj.sayHello(); // prints "Hello cocktail!" since the default value is Hello

obj.setGreeting('Ahoy');
obj.sayHello(); // prints "Ahoy cocktail!"

obj.setGreeting('Yo');
obj.sayHello(); // prints "Yo cocktail!"

````

The '@properties' annotation creates the getter and setter for you. It also adds the property to the subject (or
subject's prototype in case of classes). An special case is made when the defined property is a boolean. Here,
instead of having a getter an isXXX method is created. 

MyClass.js

````javascript

var cocktail = require('cocktail'),
    MyClass = function(){};

cocktail.mix(MyClass, {

    '@properties' : {
        greeting: 'Hello',
        silent: false
    },

    sayHello: function(){
        var greeting = this.getGreeting();
        
        if(!this.isSilent()){
            console.log(greeting + ' cocktail!');    
        }
        
    }

});

module.exports = MyClass;

````

In the example above, we have added a property _silent_ which defines if the sayHello() method will print the message
to the console or not. Since _silent_ is declared as _false_ it is assumed as a boolean property then an **isSilent** method
is created instead of **getSilent**. 
