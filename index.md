---
layout: default
title: Home
---

# What's Cocktail?
Cocktail is a small library for NodeJS to easily create and define Classes. It helps to reuse code and to maintain a simple and clear code structure. 

# For Who?
Cocktail is not for everyone. If you feel uncomfortable writing your methods and properties and using _utils_ helpers for inheritance, or you just miss having a better and organised way to read your classes definitions, or you believe that a 'util' is a code smell, then _CocktailJS_ **is for you**.

Moreover, if you like OOP and you want to go one step ahead and explore about using **traits** and **talents** to share behaviour, then you'll **love** _Cocktail_.

# Keep code simple yet reusable
_Cocktail_ is **so simple** that it has only _one_ public method: `cocktail.mix()`

**cocktail.mix** accepts two parameters: 

- **subject**: {Class|Object} 
- **options**: {Object}

In its simplest use case, **mix** will merge your subject with the options. So if you use an Object as a _subject_ and a bunch of properties as _options_, the result will be a merge:


````javascript
var obj = {};

cocktail.mix(obj, {
    aProperty: 'something',
    anotherOne: 'something else'
});

//now you can access aProperty or anotherOne from obj:
console.log(obj.aProperty); //will print 'something'
````

## What about all the other cool features?
Yes, I mentioned _inheritance_, _traits_, _talents_, blah, blah... Cocktail uses these special properties names that are defined between quotes and start with `@`, those are called `Annotations` in Cocktail.

# Annotations
So, `Annotations`, what ...? Annotations are just some meta-data Cocktail uses to apply some processes. As meta-data, they are not part of the resulting mix. Let's explore a bit more about them:

## @extends
For classical OOP inheritance we need a _Base_ class. `@extends` annotation is used to tell Cocktail that we are going to apply inheritance from a base class. 

````javascript
var cocktail = require('cocktail'),
    BaseClass = require('./MyBaseAwesomeClass'),
    MyClass = function(){};

cocktail.mix(MyClass, {
    '@extends': BaseClass,

     someProperty: 'something'
});
````


As in classical OOP inheritance, any instance of MyClass is an instance of BaseClass too, and you have access to any methods or properties defined in BaseClass.

## @properties
Properties are a kind of magical helper. All the properties defined in @properties become part of the class prototype, but also, it generates getter/setter methods for you.

````javascript
var cocktail = require('cocktail'),
    BaseClass = require('./MyBaseAwesomeClass'),
    MyClass = function(){};

cocktail.mix(MyClass, {
    '@extends': BaseClass,
    
     '@properties': {
         name: 'default name',
         valid: true
     },

     someProperty: 'something'
});
````

In the example above, we will have a `setName` and `getName` methods accessible from any MyClass instance.  
In case the property is a _boolean_ like `valid`, then we will have an `isValid` and `setValid` methods instead. 

## @traits
_Traits_ are Composable Units of Behaviour (You can read more from [this paper](http://scg.unibe.ch/archive/papers/Scha03aTraits.pdf)). Basically, a Trait is a Class, let's say a special type of Class, that has only behaviour (methods) and no state. 

A Trait can be composed with other Traits. In Cocktail -at least- you cannot extend from a Trait or a Trait cannot extend from other class.

In the following example we explore a simple functionality, where we want to know if a given instance is "testable" and to resolve that our instance should be an instance of a "Test" class.

We can create a simple Trait "Testable" which will determine if the current instance is testable:

````javascript
var TestClass = require('./SomeBaseTest'),
    Testable = function(){};

Testable.prototype.isTestable = function(){
    return this instanceof TestClass;
};

module.exports = Testable;
````

So now we can apply the Testable Trait to our class definition:

````javascript
var cocktail = require('cocktail'),
    Testable = require('./Testable'),
    MyClass = function(){};

cocktail.mix(MyClass, {
   '@traits': [Testable],

   // ... more class definitions

});

module.exports = MyClass;
````

Now we can use our class and check if it is Testable

````javascript
var MyClass = require('./MyClass'),
    instance;

instance = new MyClass();

console.log(instance.isTestable()); //false
````

This is a very simple and fairly stupid example, but here we demonstrated we don't need Cocktail to _define_ a Trait but just only to apply it.

If you have read the paper, you might noticed that sometimes conflicts may arise and there are some ways to avoid those conflicts. Those are implemented in _Cocktail_ as well. You can create _alias_ or even _exclude_ methods from the Trait. The syntax is a bit different for these cases:

````javascript
cocktail.mix(MyClass, {
   '@traits': [
       Testable,
       {
           trait: AnotherTrait,
           excludes: ['doSomething'], 
           alias: {
              'methodFoo': 'myClassMethodFoo'
           }
       }
    ],

   // ... more class definitions

});
````

## @requires
We saw that we can use any Class defined with no state as a Trait but, sometimes we need to specify that our Trait requires some methods to be defined in order to be usable. That's what `@requires` is for. Let's see an example:

Our Trait _Formatable_ requires a value where the format will be applied. Since Traits _cannot have state_, we need some way to access to the variable we want to format. So, in this case _Formatable_ requires a _getValue_ method to be defined:

````javascript
cocktail.mix(Formatable, {
    '@requires': ['getValue'],

    format: function(pattern){
         var value = this.getValue(); 
         // apply the pattern to the current value ...
         return value;
    }
});
````

Now we can apply it to a Class:

````javascript
cocktail.mix(MyClass, {
   '@traits': [Formatable],
   '@properties': {
       value: 10
   }
});
````

Note that we have not defined a `getValue()` method which is required by our trait, but have used the properties annotation which will generate the getter for us. So in this case the __Class + glue code + Trait__ trilogy is successfully completed. 

## @talents
Talents are very similar to Traits, in fact a Trait can be applied as a Talent in Cocktail. The main difference is a Talent can be applied to an _instance_. So we can define a Talent as a _Dynamically Composable Unit of Reuse_ (you can read more from [this paper](http://scg.unibe.ch/archive/papers/Ress11a-Talents.pdf)).

In a previous example we have created a Testable trait. So, what if we want now to use that behaviour only on certain instances? Well that's when Talents become super handy:

````javascript
var cocktail = require('cocktail'),
    MyClass = require('./MyAnotherClass'),
    MyTestableClass = require('./MyClassInheritsFromTestClass'),
    Testable = require('./Testable'),
    instanceA, instanceB;

instanceA = new MyTestableClass();

cocktail.mix(instanceA, {
    '@talents': [Testable]
});

console.log(instanceA.isTestable()); //true

instanceB = new MyClass();

cocktail.mix(instanceB, {
    '@talents': [Testable]
});

console.log(instanceB.isTestable()); //false
````

The same syntax we used for Traits is applicable to a Talent for _excludes_ and _alias_ functionality. 
