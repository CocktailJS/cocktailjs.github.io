---
layout: post
title: CocktailJS v0.5.0 Released
category: blog
tags: [release]
published: true
comments: true
---

We are happy to announce a new version of CocktailJS! **CocktailJS v0.5.0** is available on [npm](https://npmjs.org/package/cocktail)!

#A new version and more!
With this new release we added more functionality to `cocktail` by adding a new way to create, use and share custom annotations. We also improved the code by refactoring some internals to avoid duplicated code, fixed some issues and we have added some tests too.
But that's not all. We have created a few **CocktailJS Extensions** and you can expect more of them to be released in a near future.

<!--break-->

##Introducing **cocktail.use()**
With this new method you can add (or even override) annotations. The process is very simple. You have to define the annotation processor (it must be a class) that you can annotate with `@annotation` and register it in the processors list using `use()` method. You can read more in our documentation about [cocktail.use](/docs/#cocktail.use) and [@annotation](/docs/#@annotation) where you will find a more detailed explanation and examples.

##Extensions
We have been discussing about using node's `EventEmitter` as a delegate in a previous [post](./eventemitter-as-delegate.html). So we decided to share that implementation and also improve it a little bit to add more functionality. Our very first extension then is an **Eventable Trait** and it is already available on [npm](https://npmjs.org/package/cocktail-trait-eventable)!

> We are working to add a new section to list all the available extensions for cocktail so stay tuned!.


Feel free to give us feedback here, or just open an issue on our [github repo](http://github.com/CocktailJS/cocktail/issues)

Happy coding!