---
layout: post
title: EventEmitter as Delegate using Traits
category: blog
tags: [traits, node, javascript]
published: true
comments: true
---

One of the most used modules in NodeJS is `events`. This module exposes a class called `EventEmitter` that allows you to listen to events in your application using the `on` or `addListener` methods. You can, as well, emit or fire your own custom events using `emit`.

Commonly, you would create an instance of EventEmitter or you could extend from it when you want to give your class the ability to listen to or fire events.

<!--break-->

index.js

````javascript
var EventEmitter = require('events').EventEmitter,
    emitter;

function onExecuted(){ /* handle event here */ };

emitter = new EventEmitter();

emitter.on('executed', onExecuted);

emitter.emit('executed');

````
  
When the `executed` event is emitted, then the `onExecuted` function will be called.  

## Extending from EventEmitter
There are tons of posts about how to use EventEmitter, but basically the idea or the most common scenario in NodeJS world, is to create your class extending from EventEmitter.

Task.js

````javascript
var EventEmitter = require('events').EventEmitter,
    Task;

//....
Task = function() {
    EventEmitter.call(this);
    //....
};

Task.prototype.__proto__ = EventEmitter.prototype

//....

Task.prototype.execute: function() {
    //....
    this.emit('executed');
}

module.exports = Task;
````

So now we can have Tasks firing our custom event.

index.js

`````javascript
var Task = require('./Task'),
    task;

task = new Task();

task.on('executed', function(){/* handle event here */})

task.execute();
`````

Here, our execute method in Task fires the `executed` event.

##Compositon over inheritance
This design principle is also applicable to NodeJS modules. We want to have a inheritance tree that reflects the problem we are trying to solve, not how we solve it. Saying that a **A Task is an EventEmitter** might not have a meaning in our solution.

> Read more about this [here](http://en.wikipedia.org/wiki/Composition_over_inheritance)

##EventEmitter using Composition
If we add an EventEmitter instance property to our Task, then we can delegate the methods for adding listeners or firing event to that property.

Task.js

````javascript
var EventEmitter = require('events').EventEmitter,
    Task;

//....
Task = function() {
    this._emitter = new EventEmitter();
    //....
};

// Delegation methods:

Task.prototype.on = function() {
    this._emitter.on.apply(this._emitter, arguments);  
};

Task.prototype.emit = function() {
    this._emitter.emit.apply(this._emitter, arguments);  
};

//....

Task.prototype.execute: function() {
    //....
    this.emit('executed');
}

module.exports = Task;
````

With this change we can still execute our `index.js` file without changing anything else since we just created the EventEmitter methods as delegated ones in our `Task.js` class definition.


##Reusing code
This code is simple but, if we want to add a new class to represent a Step and we need the Step to be able to listen to events then we will ending up copying and pasting the code for the delegation methods into our Step class definition.


##Using Traits to share delegation code
All of the above applies to any class created with cocktailJS. But, we have another mechanism that helps when we want to reuse code. **Traits**.

Extracting all that code that we want to share into a Trait will be very easy: We just want to have -in this case- `on` and `emit` methods, so our Trait will be defining only those two:

Eventable.js

````javascript
var cocktail = require('cocktail');

cocktail.mix({
    '@exports' : module, 
    '@requires': ['getEmitter'],

    on: function() {
        this.getEmitter().on.apply(this.getEmitter(), arguments);
    },

    emit: function() {
        this.getEmitter().emit.apply(this.getEmitter(), arguments);
    }

}) ;
````

The `Eventable` trait requires a `getEmitter` method defined in the class where we are going to apply it. And here,  `getEmitter` is expected to return an instance of EventEmitter *or* any other object that has a similar public api. So you can implement your own event emitter if you would like to do it.

Now, we have to apply the Trait `Eventable` to our `Task` class:

Task.js

````javascript
var cocktail     = require('cocktail'),
    EventEmitter = require('events').EventEmitter,
    Eventable    = require('./Eventable');

cocktail.mix({
    '@exports': module,
    '@as'     : 'class',

    '@traits' : [Eventable],

    '@properties' : {
        emitter : undefined
    },

    constructor: function() {
        this.setEmitter(new EventEmitter());
    },

    execute: function() {
        this.emit('executed');
    }
});

````

With these changes we can still execute our `index.js` with no problems. And in case we need a new class `Step` to fire and listen to events, we can reuse the `Eventable` trait in the same way as we did for `Task`:

Step.js

````javascript
var cocktail     = require('cocktail'),
    EventEmitter = require('events').EventEmitter,
    Eventable    = require('./Eventable');

cocktail.mix({
    '@exports': module,
    '@as'     : 'class',

    '@traits' : [Eventable],

    '@properties' : {
        emitter     : undefined,
        description : 'default step description'
    },

    constructor: function() {
        this.setEmitter(new EventEmitter());
    },

    done : function() {
        this.emit('done');
    },

    check : function() {
        // do some check here ...
    }

});

````

##Even more reusable code: __@evented__ Annotation
CocktailJS relies on annotations to perform tasks over classes. It provides as well a mechanism that allows you to create your own annotations too.
In this case, we have a few steps we are doing to apply the `Eventable` trait, and those steps are the same in our `Task` and `Step` classes. We can, then, create a process to do that for us implementing a custom annotation.

Evented.js

````javascript
var cocktail  = require('cocktail'),
    Eventable = require('./Eventable'),
    Emitter   = require('events').EventEmitter;

cocktail.mix({
    '@annotation': 'evented',
    '@exports'   : module,
    '@as'        : 'class',

    '@properties': {
        parameter: undefined
    },

    process: function(subject){
        var emitter = this.getParameter();

        if(emitter) {

            cocktail.mix(subject, {
                '@traits': [Eventable],

                _emitter: undefined,

                getEmitter: function(){
                    if(!this._emitter){
                        this._emitter = (emitter === true) ? new Emitter() : emitter;
                    }

                    return this._emitter;
                }
            });

        }
    }

});

````

The `Evented` Annotation will apply the `Eventable` trait for us. If the annotation parameter is `true` then we are going to create the `EventEmitter` instance but, you can pass your own emitter instance too.

Refactoring our `Task` and `Step` classes to use the `@evented` annotation:

Task.js

````javascript
var cocktail = require('cocktail'),
    Evented  = require('./Evented');

cocktail.use(Evented);

cocktail.mix({
    '@exports': module,
    '@as'     : 'class',

    '@evented': true,

    execute: function() {
        this.emit('executed');
    }
});
````

Step.js

````javascript
var cocktail = require('cocktail'),
    Evented  = require('./Evented');

cocktail.use(Evented);

cocktail.mix({
    '@exports': module,
    '@as'     : 'class',

    '@evented': true,

    '@properties' : {
        description : 'default step description'
    },

    done : function() {
        this.emit('done');
    },

    check : function() {
        // do some check here ...
    }

});
````


##Final words
We have seen here a few design principles and some features behind cocktailJS that help to reduce and reuse code. Now it is time for you to experiment with it and let us know what's your experience, thoughts, etc.
