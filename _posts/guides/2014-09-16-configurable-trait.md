---
layout: guide
title: Constructing an Object with Configuration
published: false
category: guides
tags: [Extensions]
comments: true
---

*Configurable* is one of the Traits / Talents we have published as [CocktailJS extensions](/extensions). Here, we are going to explore what is the purpose of having a "Configurable" Class or Module.

Even if we are creating objects from a module factory or from a constructor, we may want to pass along some configuration data. This configuration is often used to be applied as the instance state.

This trait uses setters to configure the instance based on a configuration object. For any property defined in that object, the configure method will check if a setter exists and if it does then it will be invoked with the corresponding value. If there is no setter, the property is just ignored.

