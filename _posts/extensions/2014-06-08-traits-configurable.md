---
layout: post
title: Configurable
category: extensions
tags: [traits, talents]
repo-name: cocktail-trait-configurable
published: true
comments: true
---

This extension is a **Trait** to include a __configure__ method that will receive an object parameter and it will call each existent setter for each key with the corresponding value. If there is no setter for a given key, it is discarded. This is very useful for initializing objects or classes and passing a single argument into the constructor.

````bash
$ npm install cocktail-trait-configurable --save
````