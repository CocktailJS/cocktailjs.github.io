---
layout: guide
title: How CocktailJS Works.
published: false
category: guides
tags: [Intermediate]
comments: true
---

This guide is intended to describe a bit deeper how `cocktail.mix` works and how `cocktail.use` fits into the process.

#Inside cocktail.mix()

The main flow inside the mix process is basically splitted into 3 phases: 

    - determine what will be mixed and apply defaults.
    - configure and enqueue the processors for each annotation.
    - execute the processors.

The first phase will perform some checks to determine if you are trying to define a class with one single parameter, apply defaults configurations or create the default constructors.

Then, the options object is inspected. All the annotations are 