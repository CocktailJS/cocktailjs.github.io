#Repository for CocktailJS page

The CocktailJS page is built using [Jekyll](http://jekyllrb.com)

##Contributing

If you feel something is wrong, poorly worded, or you've found a typo please open an issue [here](https://github.
com/CocktailJS/cocktailjs.github.io/issues).  
Or, you can fork the repository and contribute directly by fixing the issue, adding a new post or creating a new guide. All help is greately appreciated.

##Posts & Guides

Please keep the current formatting. We use markdown for almost everything.

###Add a new Post
To add a new post, just create a new file under `_posts/blog` folder. Just keep the header as in others files:

````
---
layout: post
title: <TITLE HERE>
category: blog
tags: [<TAG1> <TAG2>]
comments: true
---

````

###Add a new Guide
For guides, create a new file under `_posts/guides` and keep the header information:

````
---
layout: guide
title: <TITLE HERE>
category: guides
tags: [<Getting Started|Intermediate>]
comments: true
---

````

Tags MUST contain at least one of the items in the tags list defined in guides/index.md


##Test it locally

Just run on the folder where you forked or cloned this repository

````bash

$ jekyll serve

````

Then open [http://localhost:4000](http://localhost:4000) on your browser.


