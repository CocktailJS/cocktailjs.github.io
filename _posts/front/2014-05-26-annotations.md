---
title: Annotations
category: front-section-2
class: annotations
---

Annotations are **meta-data** CocktailJS uses to apply some processes. As meta-data,
they are not part of the resulting mix, they are declarative and they bring readability
on your code.

> A Class Example (Car.js)

````javascript
var cocktail = require('cocktail');

cocktail.mix({
	'@exports': module,
	'@as': 'class',

	'@properties': {
		make: '',
		model: '',
		year: null
	},

	constructor: function(make, model, year) {
		this.setMake(make);
		this.setModel(model);
		this.setYear(year);
	}
});

````
Read more: [Class Definitions](/guides/using-single-parameter-class-definition.html)
