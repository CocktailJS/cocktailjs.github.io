---
layout: docs
title: Documentation
description: CocktailJS API Reference
published: true
---

<a id="cocktail.mix"></a>
#**Cocktail.mix**(_subject_, _options_);
Merges the _options_ object into the _subject_. If _subject_ is a class, then the merge will be applied to the subject's prototype.
The second parameter can specify _annotations_ that will trigger some processes over the current mix.

<a id="subject"></a>
##**subject** {Function|Object}
This is the variable where the mix will be done. It is usually a variable name, where we can reference a class or object:

- **Object**: Just a simple instance or plain object.  
        
        var obj = {};
        var another = new AnotherClass();

- **Class**: A class reference or a constructor function.  

        var MyClass = function(){};
        var Other = require('./SomeClass');

- **Trait/Talent**: Traits and Talents are special cases of Class.  

        var MyTrait = function(){};
        var TraitA = require('./TraitA');

<a id="options"></a>
##**options** {Object}  
The option object can contain any number of _methods_, _properties_ and _annotations_. All the properties and methods will be merged into the subject.

- **Property**: A key-value pair that defines "state".

        {
            someProperty: 1
        }

- **Method**: A key-function pair that defines behavior.  

        {
            someMethod: function(){}
        }

- **Annotation**: A special case of property that starts with the @ symbol, that is intended to define a process, mark, or any other metadata over the given mix.   

        {
            '@someAnnotation': 'this is not state'
        }

<a id="annotations"></a>
#**Annotations**
_Annotations_ are special properties defined in the _options_ parameter of Cocktail.mix() method.
This properties are defined by a name starting with `@`. The following is the list of the current Cocktail core annotations.

<a id="@annotation"></a>
##**@annotation**: {String} name
>Aplicable to **Class**

This annotation will register the current mix as a custom annotation by the name parameter precedeed by the @ symbol.
The annotation is applicable to a Class with a **process** and **setParameter** methods.

>Example:

        var Cocktail = require('Cocktail'),
            Custom = function(){};

        Cocktail.mix(Custom, {
            '@annotation' : 'custom',

            setParameter: function(){/*...*/},

            process: function(){/*...*/}
        });

>The Custom class is a processor for the annotation named `custom`.

<a id="@extends"></a>
##**@extends**: {Function} parent class
>Aplicable to **Class**

Makes the _subject_ inherit from the given parent class with a prototype chaining inheritance. The parent overriden class methods are accessible through
a **callSuper** method.

###**callSuper**({String} methodName, {Any} param1, ..., {Any} paramN)
It calls the specified methodName on the parent class with the given params.

>Example:

        var Cocktail = require('Cocktail'),
            Base = require('./Base'),
            MyClass = function(){};

        Cocktail.mix(MyClass, {
            '@extends' : Base,

            /**
             * overrides Base foo(param) method
             */
            foo: function(param){
                //do something here
                //...

                //call parent foo method
                this.callSuper('foo', param);
            }

            /* ... */

        });

>MyClass class extends from the Base class.

<a id="@properties"></a>
##**@properties**: {Object} properties
>Aplicable to **Class**, **Object**

This is a helper to define getters and setters for the given properties. All the properties specified here become part of the 
subject (class prototype or object) with the specified value. For any non boolean properties the **get[PropertyName]** and **set[PropertyName]** methods
are created. If the property is a boolean then an **is[PropertyName]** method is created instead of the getter.

>Example

        var Cocktail = require('Cocktail'),
            MyClass = function(){};

        Cocktail.mix(MyClass, {
            '@properties' : {
                foo: 'foo',
                total: 0,
                initialized: false,
                other: undefined
            },


            /**
             * overrides the setter for foo
             */
            setFoo: function(param){
                //do something here
            }

            /* ... */

        }); 


        var instance = new MyClass();

        //returns 'foo'
        instance.getFoo();


        //returns false
        instance.isInitialized();

        //returns undefined
        instance.getOther();

> MyClass is defined with all the properties specified in the annotation. 

<a id="@traits"></a>
##**@traits**: {Array} trait list
>Aplicable to **Class**, **Trait**, **Talent**

This annotation allows to define or specify which traits will be part of the current mix. A **Trait** is an special case of
class where you define only behavior that will be shared among other classes or even other Traits.

Traits implementation doesn't allow to override a method defined in the target class with one in the current Trait but, it defines
some mechanisms to avoid method collision:

- **alias**: If the method is already defined in the target class, you can specify an alias name for the trait method that will be used in target
class or trait to refer to it.

- **excludes**: If the method is not needed on you target class you can exclude it.

Basically, all the methods defined in the Trait will become part of the target class.

>Example

        var Cocktail = require('Cocktail'),
            TraitA = require('./TraitA'),
            TraitB = require('./TraitB'),
            MyClass = function(){};

        Cocktail.mix(MyClass, {
            '@traits' : [
                TraitA,
                {
                    trait: TraitB,
                    
                    //exclude foo method
                    excludes: ['foo'],

                    //and make myDoSmth alias on doSmth method from TraitB
                    alias: {
                        doSmth: 'myDoSmth' 
                    }
                }
            ],

            foo: function(param){
                //do something here
                //...
            }

            /* ... */

        });


<a id="@talents"></a>
##**@talents**: {Array} talent list
>Aplicable to **Object**

Talents shared the same design principles that Traits but they are applicable to an instance. 
Talents have the same mechanisms (excludes and alias) to avoid method collision as in Traits.

>Example

        var Cocktail = require('Cocktail'),
            TraitA = require('./TraitA'),
            TraitB = require('./TraitB'),
            ClassA = require('./ClassA'),
            myObj = new ClassA();

        Cocktail.mix(myObj, {
            '@talents' : [
                TraitA,
                {
                    talent: TraitB,
                    
                    //exclude foo method
                    excludes: ['foo'],

                    //and make myDoSmth alias on doSmth method from TraitB
                    alias: {
                        doSmth: 'myDoSmth' 
                    }
                }
            ]

            /* ... */

        });


<a id="@requires"></a>
##**@requires**: {Array} required method list
>Aplicable to **Trait**, **Talent**

Traits and Talents definitions have no state but, they can access state in the target class or object through some 
methods that expose the state. Or a given trait might need some other functionality defined somewhere else.
Those **required** methods are defined using a **'@requires'** annotation in the Trait or Talent definition.

> Example

        var Cocktail = require('Cocktail'),
            TraitA = require('TraitA'),
            MyTrait = function(){};


        Cocktail.mix(MyTrait, {
            '@requires': ['getData'],

            doSomethingWithData: function(){
                var data = this.getData(); //this method should be provided by the target class/object

                //do something with the data here
            }
        });    


