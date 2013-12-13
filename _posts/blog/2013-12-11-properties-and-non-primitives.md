---
layout: post
title: Properties & Non Primitive Values
category: blog
tags: [javascript, oop, properties]
published: true
comments: true
---

Javascript has five primitive data types: String, Number, Boolean, Null and Undefined. All of these are inmutable, meaning that their values cannot be changed, while Objects, Array, Date, and others are mutable. 

This post is intended to show a very subtle difference while defining properties values for a Class inside a prototype versus in the constructor.

**TL;DR** Do not define mutable properties (Object, Array, Date, etc) in the prototype.

<!--break-->

# Defining Properties: Instance vs Prototype

When defining a property for a Class, we usually see these two common ways of doing it:

> Prototype

MyClass1.js

````javascript

var MyClass = function(){}

MyClass.prototype.name = 'name';

MyClass.prototype.list = [];

MyClass.prototype.getList = function(){
    return this.list;
}


`````

versus

> Instance

MyClass2.js

````javascript

var MyClass = function(){
    this.name = 'name';
    this.list = [];    
}

MyClass.prototype.getList = function(){
    return this.list;
}

````

This might be a matter of taste you might think, but let me tell you it is **only** for primitive values. In the case of name, which is a `String` there is no problem at all, since you cannot modify the value. If you want to change it you have to modify the reference and that will be placed in the instance.

````javascript

var myObj = new MyClass();

myObj.name = 'my new name';


````

With the `list` is a bit different. When you create an instance of MyClass you would expect that `getList` retrieves an Array. In fact it does, but it is different depending on where we've defined our values.

Let's see an example:

````javascript

var all = [],
    howMany = 3,
    obj, i, j, list;


for(i = 0; i < howMany; i++){
    //create an instance
    obj = new MyClass();

    //set a name
    obj.name = 'Obj'+i;

    //add the name to its list
    obj.getList().push(obj.name);

    all.push(obj);
}

// now go thru all objects and print the list
for(i = 0; i < all.length; i++ ){
    obj = all[i];

    list = obj.getList();
    
    for(j = 0; j < list.length; j++){
        console.log('value for ' + obj.name + ' is: ' + list[j]);
    }
}

````

What's the output of the code defined above? Well, it depends on which version of MyClass we are using:

Using the Prototype version (MyClass1.js)

````
value for Obj0 is: Obj0
value for Obj0 is: Obj1
value for Obj0 is: Obj2
value for Obj1 is: Obj0
value for Obj1 is: Obj1
value for Obj1 is: Obj2
value for Obj2 is: Obj0
value for Obj2 is: Obj1
value for Obj2 is: Obj2
````

Now, using the Instance version (MyClass2.js)

````
value for Obj0 is: Obj0
value for Obj1 is: Obj1
value for Obj2 is: Obj2
````

In the Instance example, we create the `list` property when we instantiate the object since the constructor method is called. Then we have a fresh new Array every time we create an instance of MyClass. On the other hand, in the Prototype version, the array is attached directly to the `MyClass.prototype` meaning that there is only **one** version of the array, and unless you override it by assigning a new value on the instance, all instances will use the same array.


To summarize, do not define mutable properties (Object, Array, Date, etc) in the prototype if you expect each instance of your class to have its own reference.




