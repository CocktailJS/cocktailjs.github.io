---
layout: guide
title: Exploring Traits & Talents
published: true
category: guides
tags: [Getting Started, "@traits", "@talents"]
comments: true
---

This guide is intended to give you a brief explanation about using **Traits** & **Talents**. We are going to explore 
some basics features with a very simple example.

# Getting Started with Traits
**Traits** are Composable Units of Behavior. We are going to use them as a class with methods that will be mixed into
our class.

The methods specified in the Trait will be applied to the class becoming part of it as if they were defined into the
class.

From a previous guide we have been exploring some designs in our example with `Pirate` and `Person`. Let's refresh 
how they look like:

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

Imagine that we want to add a `Robot` to the class model. We want the **Robot** to be able to say hi too. One can be tempted
to reuse the same structure in **Pirate** and **Person** but, there is **no semantic relationship** between a Robot 
and Person. We cannot say a Robot is a Person just to inherit the _sayHi_ mechanism. We cannot create an
_abstract class_ to share the behavior for the same reason. 

**Traits** solve this gap. We can create a _trait_ to reuse the sayHi functionality. Let's see how it works. We are going
to create a new trait that we will call it **Greetable** and it can be defined as follows:

Greetable.js

````javascript

var cocktail  = require('cocktail'),
    Greetable = function(){};

cocktail.mix(Greetable, {
    '@requires': ['getGreeting'],

    
    sayHi: function(){
        var greeting = this.getGreeting();
        console.log(greeting + "!");
    }
});

module.exports = Greetable;

````

The code above defines a class as a _Trait_ **Greetable**. It is not so different from defining a class. We have 
specified our `sayHi` method in this trait. This method needs to access to the `greeting` variable. Since _Traits_
cannot define any state, we use the annotation `@requires` to specify that a `getGreeting` method is needed to make
the trait able to work properly.

Now we can create the `Robot` class that will be composed with the trait.

Robot.js

````javascript

var cocktail  = require('cocktail'),
    Greetable = require('./Greetable'),
    Robot     = function(){};

cocktail.mix(Robot, {
    '@traits': [Greetable],

    greeting: '01001000 01100101 01101100 01101100 01101111', //Yes, that's hello in binary :)

    getGreeting: function(){
        return this.greeting;
    }
});

````

We have created a new class for `Robot`. We've declared that our class is composed with a `Trait` **Greetable**.
The _Greetable_ needs a `getGreeting` method so we defined one that returns our greeting property.

## Refactoring our classes to use Traits

The _Person_ and _Pirate_ classes have the same behavior, so now we can **reuse** our trait functionality into them.
Let's refactor them to take advantage of the _Greetable_ trait.


Person.js

````javascript

var cocktail  = require('cocktail'),
    Greetable = require('./Greetable'),
    Person    = function(){};

cocktail.mix(Person, {
    '@traits': [Greetable],

    greeting: 'Hello',

    getGreeting: function(){
        return this.greeting;
    }
});

module.exports =  Person;

````

That's all we need. We don't need to change our `Pirate` class since the methods defined in the _trait_ will be
inherited from `Person`.

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

Let's see now how this will work into our main file.

index.js

````javascript

var Person = require('./Person'),
    Pirate = require('./Pirate'),
    Robot  = require('./Robot'),
    joe, jack, marvin;

joe = new Person();

joe.sayHi(); //will print "Hello!" in the console

jack = new Pirate();

jack.sayHi(); //will print "Ahoy!" in the console

marvin = new Robot();

marvin.sayHi(); //will print "01001000 01100101 01101100 01101100 01101111!" 

````

# Talents
We have seen how to create a Trait and how to use it into our class definition. **Talents** are not that different.
In fact, we can reuse our _Greetable_ trait definition as a _Talent_. We only need to keep in mind that our object
has to be _compatible_ with the _Talent_, this means that all the methods specified as required have to be part of
our object.  

index.js

````javascript

var Person = require('./Person'),
    Pirate = require('./Pirate'),
    Robot  = require('./Robot'),
    Greetable = require('./Greetable'),
    joe, jack, marvin, 
    dishwasher = { model: 'DISHWASHER 1000'};

joe = new Person();

joe.sayHi(); //will print "Hello!" in the console

jack = new Pirate();

jack.sayHi(); //will print "Ahoy!" in the console

marvin = new Robot();

marvin.sayHi(); //will print "01001000 01100101 01101100 01101100 01101111!" 

cocktail.mix(dishwasher,{
    '@talents': [Greetable],

    getGreeting: function(){
        return this.model;
    }
});

dishwasher.sayHi(); //will print "DISHWASHER 1000!"

````

As you can see in the example we have defined an object _dishwasher_. For some reason we want to make the dishwasher
able to sayHi too. We need a getGreeting method since it is not defined in the object. To do so, we can define it
using our mix to return the dishwasher model property. 
We composed our object with the **Greetable** trait by specifying the annotation `@talents` with the desired talent. 
As we said before a Trait can be considered as a Talent to compose objects.


