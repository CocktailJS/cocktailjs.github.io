---
layout: post
title: Traits with ES7 Decorators
category: blog
tags: [es7, traits-decorator, babel, decorator, bind-operator]
published: true
comments: true
---

In this post we are going to explore how to use ES7 features to define traits with a brand new module: `traits-decorator`.

<!--break-->

ES6 gave us the simplicity to create classes in a more declarative way. With ES6 modules we have also a better and clear definition of what we are exporting. Those aspects were a primary goal of **Cocktail**.

## Decorators

If you are not familiar with decorators, they are pretty similar to our `annotations` but they are applied to a class or method/property. The idea is that you can operate over a definition in a **declarative** way.  
Let's see an example:

```js
function Testable (target) { target.isTestable = true }

@Testable
class ATestableClass {

}

// now we can check if the class is testable
ATestableClass.isTestable // true

```

You can read more from the proposal [here](https://github.com/wycats/javascript-decorators). Also, check this post about using decorators: [Declarative vs Imperative](http://elmasse.github.io/js/decorators-bindings-es7.html)


## Introducing traits-decorator module

This new module, already available on npm, allows us to experiment with the same trait concept we use in Cocktail using ES7 decorators.

### How do we use ES7 functionality?

First, install babel:

```
[sudo] npm i -g babel
```

**Note**: If you are not familiar with ES6 syntax or how to use babel, please check the [babel site](https://babeljs.io/).

Install `traits-decorator`:

```
npm i -S traits-decorator
```

Define a Trait and a class:

> example.js

```js
'use strict'

import { traits } from 'traits-decorator'

// A Simple trait to log in the console
class TLog {
    log(...args) {
        console.log(...args);
    }
}

@traits(TLog)
class MyClass {

    constructor(...options) {
        this.log(`> constructing with ${options}` )
    }
}

let instance = new MyClass(1,2,3,4) // > constructing with 1,2,3,4

```

To run this example we can use `babel-node`, which is installed with `babel`:

```
babel-node example.js
> constructing with 1,2,3,4
```

## Bind Operator

 The **bind-operator**, another ES7 experimental feature, allow us to execute a given method as it were part of the current caller.

> example-bind-operator.js

```js
'use strict'

function logMe() { console.log(this) }

let obj = { state: 1}

obj::logMe()

```

To run this example we have to enable stage 0 features with babel, so we need to run it with --stage 0 param:

```
babel-node --stage 0 example-bind-operator.js
{ state: 1 }
```


## Solving Conflicts with traits-decorator

In case we have to solve a name conflict, by excluding or aliasing a method, we provide those as `bindings`. 

### Alias

To solve a conflict by aliasing a method we use the `alias` binding:

> alias.js

```js
'use strict'

import { traits, alias } from 'traits-decorator'

class Foo {
    foo() { console.log('Foo:foo') }
}

class FooBar {
    foo() { console.log('FooBar:foo') }
    bar() { console.log('FooBar:bar') }
}

@traits(Foo, FooBar::alias({foo: 'aliasFoo'}))
class MyClass {
    //...
}

let instance = new MyClass()

instance.foo() // Foo:foo
instance.aliasFoo() // FooBar:foo
instance.bar() // FooBar:bar
```

To run this `alias.js` you need the --stage 0 param as decribed in the previous example.

### Exclusion

In a similar way we can use `excludes` binding to remove the conflict:

> excludes.js

```js
'use strict'

import { traits, excludes } from 'traits-decorator'

class Foo {
    foo() { console.log('Foo:foo') }
}

class FooBar {
    foo() { console.log('FooBar:foo') }
    bar() { console.log('FooBar:bar') }
}

@traits(Foo, FooBar::excludes('foo'))
class MyClass {
    //...
}

let instance = new MyClass()

instance.foo() // Foo:foo
instance.bar() // FooBar:bar
```

Run this example with --stage 0 param.

### Combine alias and excludes

We can chain `alias` and `excludes`:

```js
@traits(TExample::excludes('foo','bar')::alias({baz:'exampleBaz'}))
class MyClass {}
```

There is another binding `as` to define exclusions and aliases in one operation:

> combine-alias-excludes.js

```js
'use strict'

import { traits, as } from 'traits-decorator'

class Foo {
    foo() { console.log('Foo:foo') }
}

class FooBar {
    foo() { console.log('FooBar:foo') }
    bar() { console.log('FooBar:bar') }
}

@traits(Foo, FooBar::as({excludes:['foo'], alias: {bar: 'fooBar'}}))
class MyClass {
    //...
}

let instance = new MyClass()

instance.foo() // Foo:foo
instance.fooBar() // FooBar:bar
```

Again, run the example with --stage 0 param.

## Compatibility with Cocktail Traits

We can use any of the current traits defined using `cocktail` with `traits-decorator`. The following example will showcase `cocktail-trait-eventable` applied with `traits` decorator:


```js
'use strict';

import { traits } from 'traits-decorator'

import Eventable from 'cocktail-trait-eventable'
import EventEmitter from 'events'


@traits(Eventable)
class Evented {

    constructor() {
        this._emitter = new EventEmitter()
    }

    //glue code for Eventable trait
    getEmitter() {
        return this._emitter
    }

    emitFoo() {
        this.emit('foo')
    }
}

let evented = new Evented()

evented.on('foo', () => console.log('whooaa'))

evented.emitFoo()

```


## Feedback required!

While this is in experimental stage, we have room for improvements. Would you start using ES7 features today? Decorators and bind-operators are very useful features that would help in terms of readability. 
All feedback is welcome. Please leave a comment or create a new issue in the [github repository](https://github.com/CocktailJS/traits-decorator/issues). 

Happy coding!