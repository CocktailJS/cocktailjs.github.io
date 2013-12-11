---
layout: post
title: Why Annotations?
category: blog
tags: [annotations, node, javascript]
published: true
comments: true
---

#It's all about describing code

> An annotation is a note that is made while reading any form of text.

**Annotations** are used as a special form of syntactic metadata in the source code. 
The term **metadata** refers to "data about data". While it is an ambiguous definition, the concept in CocktailJS is 
used to describe actions performed over a class or object . We need "metadata" to perform those actions but at the same time they
describe -or annotate- the code. 

<!--break-->

Let's see an example:

````javascript

cocktail.mix(MyClass, {
    '@extends': Base,
    '@properties': {
        text: 'some text'
    },

    //...
});
````

In the example above, we've annotated MyClass with two annotations, '@extends' and '@properties'. MyClass _extends_ from _Base_,
and it has a _property_ named _text_.

Basically, any property that starts with **@** defined in the second parameter in _cocktail.mix_ method, is treated as an annotation.
cocktail.mix() will read every annotation and perform the associated action.

##The '@' symbol
The **@** symbol is not a valid indentifier. We have to define the annotations between quotes:

````javascript

'@extends': Base,

````

This is not just a workaround. When you read code you can easily detect a property name defined between quotes. It gives 
the sensation that those properties don't belong to the code, which is the idea behind the annotation concept.

##Advantages 

- **Extensibility**: cocktail is designed to add as many annotations processors as you need.

- **Semantic**: Since we have annotations to define properties, inheritance, merge, etc., that metadata doesn't become part of the object or class. 
It is right there in your code to help you to describe it. Helping to create methods for you, inheriting from another class or defining a merge mechanism, but,
your final result will be as clean as if you were using an util method.

- **Simplicity**: With one single action - **mix** - you can extend, add setter/getters, merge data, etc.

- **Readability**: One single action makes your code easy to read. Instead of having a set of instructions to inherit, merge, apply a Trait, etc.,
all these actions are performed in one single method.

These are some of the advantages of having annotations in cocktail and they are part of the library goals.

##Do one thing and do it well
cocktail delegates all the work to its annotations so each of them can do a very simple and small action, even though sometimes it is not that small or simple.  
The goal behind a particular annotation should be concise and very well defined.

