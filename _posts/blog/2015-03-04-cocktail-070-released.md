---
layout: post
title: CocktailJS v0.7.0 Released
category: blog
tags: [release]
published: true
comments: true
---

A new version of CocktailJS! **CocktailJS v0.7.0** is available on [npm](https://npmjs.org/package/cocktail)!
As with all other releases, we try to keep all functionality backward compatible until the very first of 1.0 is released.

<!--break-->

#What's New
In this version we have added the possibility to define `traits` and `talents` not only as classes but also as *objects*. The code has been also improved by reducing some complex methods and internal refactoring was made to keep a cleaner and readable code.

Grunt was also something that was adding unnecessary overhead. In fact, it was only used to run tests and linter which can be easily done using npm scripts avoiding more dev dependencies.

## Traits and Talents with objects
As a little example, we can now redefine our [enummerable example](https://github.com/CocktailJS/cocktail-recipes/tree/master/enumerables)

> arrayAsItems.js

```js
'use strict';

var cocktail = require('cocktail');

cocktail.mix({
    '@exports': module,
    '@as': 'class',

    getItems: function () {
        return this;
    }
});


```

Since this is just a simple method we use to share our glue code for `Enummerable` trait, it is pretty simple. With CocktailJS v0.7.0 we can define this as:

> arrayAsItems.js

```js

module.exports = {
    getItems: function () {
        return this;
    }
};

```

And then it can be used in `index.js` without modifying a single line of code:

> index.js

```js
'use strict';

var cocktail            = require('cocktail'),
    enumerable          = require('./enumerable'),
    arrayAsItems        = require('./arrayAsItems'),
    list                = require('./list'),
    asEnumerableArray   = require('./asEnumerableArray');

var myList, myOtherList, myArr, myOtherArr, enumArr;

myArr = ['one', 'two', 'three'];
myOtherArr = ['four', 'five', 'six'];

// List
console.log('-- list:');

// using factory method create()
myList = list.create({items: myArr});

console.log(myList.first()); // one
console.log(myList.last());  // three

//using factory method withArray()
myOtherList = list.withArray(myOtherArr);

console.log(myOtherList.first()); // four
console.log(myOtherList.last());  // six


//Arrays
console.log('-- arrays:');

// Array with enumerable
cocktail.mix(myArr, {
    '@talents': [enumerable],

    getItems: function () {
        return this;
    }
});

console.log(myArr.first()); // one
console.log(myArr.last());  // three

// Array with enumnerable and arrayAsItems

cocktail.mix(myOtherArr, {'@talents': [enumerable, arrayAsItems]});

console.log(myOtherArr.first()); // four
console.log(myOtherArr.last());  // six


// Array with asEnumerableArray
enumArr = ['seven', 'eight', 'nine'];

cocktail.mix(enumArr, {'@talents': [asEnumerableArray]});

console.log(enumArr.first()); // seven
console.log(enumArr.last());  // nine


```

Or we can even define this `arrayAsItems` right into our index.js as an Object:

> index.js

```js
//...

var cocktail            = require('cocktail'),
    enumerable          = require('./enumerable'),
    list                = require('./list'),
    asEnumerableArray   = require('./asEnumerableArray');

var arrayAsItems = {
    getItems: function () {return this;}
};

//...

```

Feel free to give us feedback here, or just open an issue on our [github repo](http://github.com/CocktailJS/cocktail/issues)

Happy coding!
