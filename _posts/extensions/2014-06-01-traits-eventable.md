---
layout: post
title: Eventable
category: extensions
tags: [traits, talents]
repo-name: cocktail-trait-eventable
published: true
comments: true
---

Eventable provides a trait to use an Emitter as a delegate. It exposes the event emitter api as part of the host class or module. It uses a given Emitter instance through a required method _getEmitter_ to register listeners and emit events.

````bash
$ npm install cocktail-trait-eventable --save
````