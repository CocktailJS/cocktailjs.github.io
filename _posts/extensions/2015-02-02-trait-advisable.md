---
layout: extension
title: Advisable
category: extensions
tags: [traits, talents]
repo-name: cocktail-trait-advisable
published: true
comments: true
---

A Trait to add **AOP advices** into Classes/Objects. The methods `around`, `after` and `before` are available on host classes or objects.

<!--break-->


###Install

````bash
$ npm install cocktail-trait-advisable --save
````

### Usage

Define a class with advisable trait:

>MyClass.js

```javascript

var cocktail = require('cocktail'),
    advisable = require('cocktail-trait-advisable');

cocktail.mix({
    '@exports': module,
    '@as'     : 'class',

    '@traits' : [advisable],

    aMethod: function () {
        console.log('a method is called!');
    }
});


```

And then use it on your index.js

>index.js

```javascript

var MyClass = require('./MyClass');



var obj = new MyClass();

function afterFn() { console.log('this will be called after!'); }

// #1 attach the after advice
obj.after('aMethod', afterFn);


// #2 call the adviced method
obj.aMethod(); 

```

On \#1 we attached the advice for after calling `aMethod` method in our `obj`. Then when \#2 is executed the console output will show:

```bash

node index.js
a method is called
this will be called after!

```