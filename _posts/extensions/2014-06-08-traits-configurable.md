---
layout: extension
title: Configurable
category: extensions
tags: [traits, talents]
repo-name: cocktail-trait-configurable
published: true
comments: true
---

This extension is a **Trait** to include a __configure__ method that will receive an object parameter and it will call each existent setter for each key with the corresponding value. If there is no setter for a given key, it is discarded. This is very useful for initializing objects or classes and passing a single argument into the constructor.

<!--break-->

###Install

````bash
$ npm install cocktail-trait-configurable --save
````

###Usage

> User.js

````javascript
var cocktail = require('cocktail'),
    Configurable = require('cocktail-trait-configurable');

cocktail.mix({
   '@exports': module,
   '@class': 'class',
   '@traits': [Configurable],

   '@properties': {
        firstName: undefined,
        lastName: undefined
    },

  constructor: function (options) {
      this.configure(options); // configure method from Configurable
  },

  setName: function (name) {
    var parts = name.split(' '),
        last, first;

      if(parts && parts.length){
        last = parts.pop();
        first = parts.join(' ');

        this.setFirstName(first);
        this.setLastName(last);
      }
  }
});

````

The User class defined here, has two properties, and since we used the `@properties` annotation to define them, we get the setters (`setFirstName` and `setLastName`) and getters (`getFirstName` and `getLastName`) methods for each of them. We have defined a third setter (setName). This setter will be called when we pass a `name` as a property. In this case, the setter it's a sort of helper to split the name into first and last name.

>index.js

````javascript
var User = require('./User'),
    john, mike;

// create user using first and last name
john = new User({firstName: 'John', lastName: 'Doe'});

console.log(john.getFirstName()); // John

// or create user using the name setter helper
mike = new User({name: 'Mike Smith'});

console.log(mike.getFirstName()); // Mike

````


