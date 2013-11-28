---
layout: guide
title: Using Custom Annotations
published: false
category: guides
tags: [Intermediate]
comments: true
---

Here we are going to explore how to define and use your own annotations. CocktailJS is very modular and it was designed to allow you to create your own annotations and even override any of the default ones.

#Introduction
An annotation is a simple task that will be performed over a Class or Object. It must be a Class that implements at least two methods:

    - setParameter(value): This method will be called with the value passed in the annotation.
    - process(subject): This method will be called when the annotation is processed and the current subject in `cocktail.mix` will be passed as paramter. 


#Create an Annotation
Let's imagine we want an annotation to indicate 

