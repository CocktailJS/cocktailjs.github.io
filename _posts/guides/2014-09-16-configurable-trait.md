---
layout: guide
title: Constructing an Object with Configuration
published: true
category: guides
tags: [Extensions]
comments: true
---

*Configurable* is one of the Traits / Talents we have published as [CocktailJS extensions](/extensions). Here, we are going to explore what is the purpose of having a "Configurable" Class or Module.

Even if we are creating objects from a module factory or from a constructor, we may want to pass along some configuration data. This configuration is often used to be applied as the instance state.

The Configurable trait uses setters in the host class to apply the values into the instance based on a configuration object. For any property defined in that object, the configure method will check if a setter exists in the host class and if it does then it will be invoked with the corresponding value. If there is no setter, the property is just ignored.

There are a few reasons to use a single object argument constructor or factory:

###Named parameters
In vanilla JavaScript there are no named parameters. When using a single object parameter it gives us a way to understand what is what in an argument list. Consider this example:

```javascript

    function Node(name, expanded, checked, visible) { /* Node Constructor Code */ }

    // ...
    
    var node = new Node('Element', true, false, true);

```

When we want to create a new Node, we need to remember what is what in the argument list. When those arguments are boolean values the code becomes a bit harder to understand.
Let's see how this is different with single object parameter:

```javascript

    function Node(options) { /* Node Constructor Code */ }

    // ...
    
    var node = new Node({
                    name: 'Element',
                    expanded: true,
                    checked: false,
                    visible: true
                });

```

###The optional parameters can be easily ignored
Using the previous Node example, if we want to make `expanded` and `checked` optional, the multiple arguments constructor becomes even worst:

```javascript

    function Node(name, expanded, checked, visible) { /* Node Constructor Code */ }

    // ...
    
    var node = new Node('Element', undefined, undefined, true);

```

While using the single object parameter it becomes just as easy as removing the optional parameters:

```javascript

    function Node(options) { /* Node Constructor Code */ }

    // ...
    
    var node = new Node({
                    name: 'Element',
                    visible: true
                });

```

###The order we pass the parameters doesn't matter
Again, since we use an object the order of the keys does not matter:

```javascript

    
    var node = new Node({
                    checked: false,
                    name: 'Element',
                    expanded: true,                    
                    visible: true
                });

```

## Using the Configurable Trait

As we saw, there are some advantages of using a constructor with a single object parameter. The Configurable trait exposes a `configure` method which receives an object and it will call the setter for each key. Using this trait in conjuction with the `@properties` annotation gives us a quite easy way to configure our objects with almost zero cost.

Let's start first by adding the Configurable trait as a dependency:

```bash

npm install cocktail-trait-configurable --save

```

Add the require for **cocktail** and **configurable** inside our module definition.

> Node.js

```javascript
var cocktail =  require('cocktail'),
    configurable = require('cocktail-trait-configurable');

cocktail.mix({
    '@exports': module,
    '@as'     : 'class',

    '@traits' : [configurable],

    '@properties': {
        name: '',
        checked: false,
        expanded: false,
        visible: false,
    },

    constructor: function (options) {
        this.configure(options);
    }

});

```

The `@properties` annotation will create getters and setters for each property. So that is all the code you need to have a configurable Node class.

> index.js

```javascript
var Node = require('./Node');

var node = new Node({name: 'Element', visible: true});

console.log(node.getName()); // Element

```


