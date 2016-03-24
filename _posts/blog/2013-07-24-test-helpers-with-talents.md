---
layout: post
title: Refactoring Test Helper Methods with Talents
category: blog
tags: [talents, node, javascript]
published: true
comments: true
---

We consider tests mandatory in CocktailJS repository, not only __unit tests__ but __integration tests__ as well. And with those tests we found ourselves having to create some _test helper methods_ to create some possible scenarios and restore state for example. We added some comments in  `lib/cocktail.js` file as a _friendly reminder_ that those methods _should_ go away when we find a better design or strategy.

<!--break-->

```javascript

    // -- >Experimental
    
    // --- This methods are used for testing, find a better desing to avoid them
    
    restoreDefaultProcessors: function() {
        var key,
            DEFAULT_PROCESSORS = cocktail._DEFAULT_PROCESSORS;

        this.setProcessors({});
        
        for(key in DEFAULT_PROCESSORS){
            if(DEFAULT_PROCESSORS.hasOwnProperty(key)) {
                this._processors[key] = DEFAULT_PROCESSORS[key];
            }
        }
        
    },

    clearProcessors: function() {
        var processors = this.getProcessors(),
            key;
        for(key in processors){
            if(processors.hasOwnProperty(key) && key !== 'no-op'){
                delete processors[key];
            }
        }
    },

    // -- >End of Experimental

```

## Talents

So, what we can do to remove those methods from the code? __Talents__ was the response. A Talent is a special case of class where we define __only__ behavior and they cannot contain any state making the _mix_ safer (There are other rules as well that prevent to override existing methods).

## Refactoring with Talents

We were using the `restoreDefaultProcessors` method in our tests so we needed to extract the method in a _Talent_ that we named `RestoreProcessor` - since it is a Talent, it is not necessary to name it as a noun, after all it should describe which behavior is provided by the Talent.

We mentioned that our Talent will be used only as a _Test Helper_, so it won't be shipped as part of the library. That being said, the Talent was placed into our `test/helper` folder.

test/helper/RestoreProcessor.js

```javascript
var cocktail = require('../../lib/cocktail');

cocktail.mix({
	'@exports': module,

	restoreDefaultProcessors: function () {
		//TODO ... 
	}
});

```

Now we need to declare what methods will be required to be defined in the object where we are going to add the Talent. Our Talent cannot define any state, so we need to specify how we can access the state using the `@requires` annotation. In this case, we need access to the _default processor list_ and the _current processors_ in order to restore them.

test/helper/RestoreProcessor.js

```javascript
var cocktail = require('../../lib/cocktail');

cocktail.mix({
	'@exports': module,
	
	'@requires': [
		'setProcessors',
		'getDefaultProcessors'
	],

	restoreDefaultProcessors: function () {
		//TODO ... 
	}
});

```

After defining the required methods, we can now start to refactor our method inside the Talent:

test/helper/RestoreProcessor.js

```javascript
var cocktail = require('../../lib/cocktail');

cocktail.mix({
	'@exports': module,
	
	'@requires': [
		'setProcessors',
		'getDefaultProcessors'
	],

	restoreDefaultProcessors: function () {
		var key,
            defaultProcessors = this.getDefaultProcessors(),
            processors = {};

        for(key in defaultProcessors){
            if(defaultProcessors.hasOwnProperty(key)) {
                processors[key] = defaultProcessors[key];
            }
        }

        this.setProcessors(processors);

	}
});

```

Almost done! The Talent is defined already and we have to apply it to the cocktail instance in our test. We need to require the Talent and apply it. 

test/integration/cocktail.js

```javascript

//...

var chai = require("chai"),
	//...
    cocktail = require('../../lib/cocktail'),
    RestoreProcessors = require('../helper/RestoreProcessors');

    //...
    cocktail.mix(cocktail, {
    	'@talents': [RestoreProcessors]
	});

	//...

	 afterEach(function(){
        cocktail.restoreDefaultProcessors();
    });

    //...

```

One last step before we run our code is to _remove_ the current definition in `lib/cocktail.js` file first because we don't need it anymore, and more importantly because it will fail if we apply a Talent with a method definition that is overriding an existing method in the target object.

