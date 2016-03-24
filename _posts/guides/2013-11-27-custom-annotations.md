---
layout: guide
title: Using & Defining Custom Annotations
published: true
category: guides
tags: [Intermediate]
comments: true
---

Here we are going to explore how to define and use your own annotations. CocktailJS is very modular and it was designed to allow you to create your own annotations and even override any of the default ones.

#Introduction
An annotation is a simple task that will be performed over a Class or Object based on a given parameter. To create a custom annotation we need a Processor Class. This Processor Class must implement two public methods:
 
- **setParameter(value)**: This method will be called with the value passed in the annotation.
- **process(subject, options)**: This method will be called when the annotation is processed and the current subject and options in `cocktail.mix` will be passed as parameters. It is important to note that `options` parameter will not contain any annotation properties that are not marked as retained.

An optional property named `priority` is used to indicate when the annotation processor will be executed in the current mix queue. See more about this in the [annotation documentation](/docs/#@annotation).

# A Simple Logger Annotation

The main goal for our custom annotation will be to add the `log(params)` method into the host class/object and use a logger instance to display the messages.

Basically we want to make the following code work:

```javascript

//...

cocktail.mix({
    '@exports': module,
    '@as'     : 'class',

    '@logger' : console,

    doSomethingAndLog: function(){
        // (1)
        this.log('doSomethingAndLog has been called!');
    } 
});

```

In the code above, we have defined a Class with a `@logger` annotation and passed to it the `console` instance. We want to be able to use that console and print the message we send to log method in (1).

#Create an Annotation Processor Class

Let's start creating our Annotation Processor. We will use the `@annotation` annotation to declare the name which will be `logger` and export it as class. 

Logger.js - initial

```javascript
var cocktail = require('cocktail');

cocktail.mix({
    '@annotation': 'logger',
    '@exports'   : module,

    setParameter: function(parameter) {},

    process: function(subject, options) {}

});

```

That's all we need. We've just defined our Logger Processor. Now, let's start working on the `process` and `setParameter` methods.

## Setting The Parameters

The `setParameter` will be called with the value of the annotation in the mix. In our example we have defined:

```javascript

    '@logger' : console,

```

So the `setParameter` will receive the `console`. In order to have access to it from the `process` method we might need to save it somewhere.

Logger.js - setting params

```javascript
var cocktail = require('cocktail');

cocktail.mix({
    '@annotation': 'logger',
    '@exports'   : module,

    _parameter: undefined,

    setParameter: function(parameter) {
        this._parameter = parameter;
    },

    process: function(subject, options) {}

});

```

## Working on the Process

Finally we need to start working into our `process` method. As mentioned before the first parameter will be the current subject. In our example we are using a single parameter class definition, so the subject will be the current Class definition. 
Our Logger Processor will add a `log()` method and use the given logger to show the message in the console (in this case).

Logger.js - process

```javascript
var cocktail = require('cocktail');

cocktail.mix({
    '@annotation': 'logger',
    '@exports'   : module,

    _parameter: undefined,

    setParameter: function(parameter) {
        this._parameter = parameter;
    },

    process: function(subject, options) {
        var logger = this._parameter;

        subject.prototype.log = function(){
            logger.log.apply(logger, arguments);
        };
    }

});

```

Last step. We said we wanted an annotation that can be processed on a Class or Object. With the code above we have added the log method to the subject prototype. We need to slightly change that code to make sure that `log` method will be added as well if the subject is an Object.

Logger.js - final

```javascript
var cocktail = require('cocktail');

cocktail.mix({
    '@annotation': 'logger',
    '@exports'   : module,

    _parameter: undefined,

    setParameter: function(parameter) {
        this._parameter = parameter;
    },

    process: function(subject, options) {
        var logger = this._parameter,
            host   = subject.prototype || subject;

        host.log = function(){
            logger.log.apply(logger, arguments);
        };
    }

});

```

The `host` variable will pick the prototype (if subject is a Class) or the subject itself if it is an object instance.

## Using our Custom Logger Annotation

Now we are going to modify our code so we can declare our Logger class and register it with cocktail instance.

MyClass.js

```javascript

var cocktail = require('cocktail'),
    Logger   = require('./Logger'); // (1)

cocktail.use(Logger); // (2)

cocktail.mix({
    '@exports': module,
    '@as'     : 'class',

    '@logger' : console, // (3)

    doSomethingAndLog: function(){
        // (1)
        this.log('doSomethingAndLog has been called!');
    } 
});

```
In the code for MyClass we have declared a variable Logger that is our recently created Annotation (1). Then we need to tell `cocktail` instance to use our custom annotation (2) by adding it to the list of processors. And finally we can use it into our mix (3).

Then we can create an index.js file to see it in action.

index.js

```javascript

var MyClass = require('./MyClass'),
    obj;

obj = new MyClass();

obj.doSomethingAndLog();

```

Executing the file on the console we should see:

```
$ node index.js
doSomethingAndLog has been called!
```

**Note**: For the sake of simplicity we are not validating the parameter has a log method that can be called nor the subject is null or any other possible error.
