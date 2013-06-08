---
layout: guide
title: Using Single Parameter Class Definition
published: true
category: guides
tags: [Getting Started, @extends, @exports, @properties, @traits, @requires]
---

This guide explains how to use the single parameter class definition to define or extend a class. It will show you how
it can be used together with `@exports` annotation.

#Creating a Class
In a previous guide [(Extending a Class)](/guides/extending-a-class.html) we have seen how to create and extend a class. Now,
we are going to revisit that example but this time we will define the class without having to specify the constructor
outside the `Cocktail.mix()` method:

Person.js

    var Cocktail = require('Cocktail');

    Cocktail.mix({
        //export the class 
        '@exports' : module,

        //declare the properties
        '@properties' : {
            greeting : 'Hello'
        },

        //the constructor is defined inside the mix()
        constructor: function(greeting) {
            if(greeting) {
                this.setGreeting(greeting);
            }
        }

    });

The code above shows how a class is defined. We have specified a `greeting` property and a constructor that receives a 
parameter for the greeting property. If the property has value then we are going to use the setter to assign the constructor
parameter to the greeting property.
Another annotation is used in this declaration. `@exports` allows to define that we are going to export the current mix. 
It will take the result of the mix and assign it to the `module.exports` variable so we don't have to create another
variable name for the class and export it or assign the Cocktail.mix execution to the `module.exports` variable.

#Extending your Class
The Single Parameter Class Definition can be applied when you pass the subject as a Object literal with a constructor
definition as we saw before. `@extends` annotation has the same effect. If you want to extend from a class `Person` 
you can use the single parameter class definition and use the `@exports` annotation as follows:

Pirate.js

    var Cocktail = require('Cocktail'),
        Person   = require('./Person');

    Cocktai.mix({
        '@exports' : module,
        '@extends' : Person,

        greeting: 'Ahoy'
    });


#Traits and Talents
We have said that _Traits & Talents_ are just a special type of class where you only define behavior. It is very common
that a Trait will require a method to be defined in order to work with your class. 
We are going to define the _Greetable_ trait using the Single Parameter Class Definition and then apply it to our `Person` class.

Greetable.js

    var Cocktail = require('Cocktail');

    Cocktail.mix({
        '@exports' : module,

        '@requires' : ['getGreeting'],


        sayHi: function(){
            var greeting = this.getGreeting();
            console.log(greeting + "!");
        }}

    });


And now we can refactor our previous `Person` class:

Person.js

    var Cocktail  = require('Cocktail'),
        Greetable = require('./Greetable.js');

    Cocktail.mix({
        '@exports' : module,
        '@traits'  : [Greetable],
        '@properties' : {
            greeting : 'Hello'
        },

        constructor: function(greeting) {
            if(greeting) {
                this.setGreeting(greeting);
            }
        }

    });

Notice that our _Greetable_ trait declares a `getGreeting` as a required method. We are applying the trait to our 
`Person` class. We didn't define the getter for `greeting` property but it is created when we declare `@properties`
annotation. 