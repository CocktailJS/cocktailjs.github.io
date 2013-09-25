---
layout: guide
title: Quick Start
published: true
category: guides
tags: [Getting Started]
comments: true
---

This guide provides very basic steps to create a project, add CocktailJS as a dependency and run an extremely simple example that will print a message in the console.

# Create a new project

The very first thing we need is to have node installed ([install node from here](http://nodejs.org/download)). Then we can 
create a new project by creating a new folder like `my-mix` for example.

````console
$ mkdir my-mix
$ cd my-mix
````

Inside this new folder we need to define a project or package descriptor as in any other node module. This file is 
named `package.json` and it should be placed at the root of your project.
You can create it manually with the following example:

package.json

````javascript
  {
    "name": "my-mix",
    "description": "my first project with cocktailjs",
    "version": "0.0.0",
    "private": true,
    "dependencies":{
      "cocktail": "*"
    }
  }        
````

Or you can use `npm` to create a new project:

````console
$ npm init
````

# Install CocktailJS as a dependency

The package.json file structure defines some aspects of our project. The more important in this case is our depencies list.
All the modules described as depencies will be installed in your project using `npm`.

````console
$ npm install
````

Or you can run

````console
$ npm install cocktail --save
````

This will install the latest version of cocktail and save the dependency in the package.json for you.

Once all the dependencies are installed, in this case only `cocktail`, your project folder structure should look
like this:

    my-mix
     \- index.js
     \- node_modules
        \- cocktail
     \- package.json


We have created our project sucessfully. Now we can edit `index.js` to start using CocktailJS.


# Hello Cocktail!

Once we have CocktailJS installed as a module into our project, we can edit `index.js` to start experimenting 
with _Cocktail.mix()_ method:

index.js

````javascript

var Cocktail = require('Cocktail'),
    myObject = {};

Cocktail.mix(myObject, {

    sayHello: function(){
        console.log('Hello Cocktail!');
    }
});

myObject.sayHello();
````

Save the file and execute it:

````console
$ node index.js
Hello Cocktail!
````
**Congratulations!** We just created a very simple first example.
