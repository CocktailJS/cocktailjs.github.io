---
layout: docs
title: Documentation
description: CocktailJS API Reference
published: true
---

<a id="cocktail.mix"></a>
#**Cocktail.mix**(_subject_, _options_);
>Since **v0.0.1**

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

>Example with a Class (Function):

MyClass.js

        var Cocktail = require('Cocktail'),
            MyClass = function(){};

        Cocktail.mix(MyClass, {

            _aProperty: 'My Property Content',

            /**
             * @public foo
             */
            foo: function(){
                console.log('foo method called!');
            }
        });

        module.exports = MyClass;

>Example with an Object:

index.js

        var Cocktail = require('Cocktail'),
            ClassA = require('./ClassA'),
            instance;

        instance = new ClassA();    

        Cocktail.mix(instance, {

            _aProperty: 'My Property Content',

            /**
             * @public foo
             */
            foo: function(){
                console.log('foo method called!');
            }
        });


<a id="annotations"></a>
#**Annotations**
_Annotations_ are special properties defined in the _options_ parameter of Cocktail.mix() method.
This properties are defined by a name starting with `@`. The following is the list of the current Cocktail core annotations.

<a id="@annotation"></a>
##**@annotation**: {String} name
>Aplicable to **Class**

>Since **v0.0.3**

This annotation will register the current mix as a custom annotation by the name parameter precedeed by the @ symbol.
The annotation is applicable to a Class with a **process** and **setParameter** methods.

>Example:

Custom.js

        var Cocktail = require('Cocktail'),
            Custom = function(){};

        Cocktail.mix(Custom, {
            '@annotation' : 'custom',

            setParameter: function(){/*...*/},

            process: function(subject){/*...*/}
        });

        module.exports = Custom;

>The Custom class is a processor for the annotation named `custom`.
>Now we can use our newly created annotation in our code:

index.js

    // 1.-include Custom annotation definition
    require('./Custom.js');

    var Cocktail = require('Cocktail'),
        myObj = {};

    Cocktail.mix(myObj, {
        // 2.-annotate the code with the custom annotation  
        '@custom' : 'some-custom-parameter',

        //more annotations, or code
    });

>In the index.js file we are first adding the annotation definition (it is not necessary to bind it to any local variable)
and then we just use our custom annotation in our code. 
The parameter specified in the mix for @custom is the one that will be passed to setParameter method in Custom Annotation definition.
Then the process() receives the subject, myObj in this case, when it is processed by the annotation.

<a id="@merge"></a>
##**@merge**: {String} merge strategy
>Aplicable to **Class**, **Object**, **Trait**, **Talent**

>Since **v0.0.4**

The merge strategy defines how the properties into the subject will be merged. The default strategy `single` is applied everytime
you execute a mix(). The `single` strategy is equivalent to `mine`.

As convention for merge strategies the second parameter is `mine` and the subject is `their`

###Merge Strategies
The following strategies are available to use as `@merge` annotation parameter:

- **single**: It will perform a single merge, the properties and functions defined in options will be added to the subject.
Properties and function with the same name are overriden with the ones defined in options parameters.
- **mine**: Same as `single`, just for keeping naming conventions.
- **their**: The merge is done as a single merge but if the property is already defined in the subject it is not overriden.
- **deep-mine**: If the property is an Object and it is already defined in the subject it gets merged using the `mine` strategy.
If the property is an Array it is concatenated with the subject version.
- **deep-their**: If the property is an Object and it is already defined in the subject it gets merged using the `their` strategy.
If the property is an Array it is concatenated with the subject version.

>Example:

index.js

        var Cocktail = require('Cocktail'),
            myObject = {
                property: {
                    a: 'a',
                    b: 'b'
                },
                values: [1,2]
            };

        Cocktail.mix(myObject, {
            property: {
                z: 'z'
            }

        });

> Here the default merge strategy is applied (`mine`). So the myObject.property becomes `{z: 'z'}`

index.js

        var Cocktail = require('Cocktail'),
            myObject = {
                property: {
                    a: 'a',
                    b: 'b'
                },
                values: [1,2]
            };

        Cocktail.mix(myObject, {
            '@merge': 'their',
            property: {
                z: 'z'
            }

        });

> Here the `their` merge strategy is applied. So the myObject.property becomes `{a: 'a', b: 'b'}`

index.js

        var Cocktail = require('Cocktail'),
            myObject = {
                property: {
                    a: 'a',
                    b: 'b'
                },
                values: [1,2]
            };

        Cocktail.mix(myObject, {
            '@merge': 'deep-mine',
            property: {
                a: 'A',
                z: 'z'
            },
            values: [3,4]

        });

> Here the `deep-mine` merge strategy is applied. So the myObject.property becomes `{a: 'A', b: 'b', z: 'z'}` and
myObject.values gets concatenated becoming `[1,2,3,4]`.

index.js

        var Cocktail = require('Cocktail'),
            myObject = {
                property: {
                    a: 'a',
                    b: 'b'
                },
                values: [1,2]
            };

        Cocktail.mix(myObject, {
            '@merge': 'deep-their',
            property: {
                a: 'A',
                z: 'z'
            },
            values: [3,4]

        });

> Here the `deep-their` merge strategy is applied. So the myObject.property becomes `{a: 'a', b: 'b', z: 'z'}` and
myObject.values gets concatenated becoming `[1,2,3,4]`.


<a id="@extends"></a>
##**@extends**: {Function} parent class
>Aplicable to **Class**

>Since **v0.0.1**

Makes the _subject_ inherit from the given parent class with a prototype chaining inheritance. The parent overriden class methods are accessible through
a **callSuper** method.

###**callSuper**({String} methodName, {Any} param1, ..., {Any} paramN)
It calls the specified methodName on the parent class with the given params.

>Example:

MyClass.js

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

        module.exports = MyClass;

>MyClass class extends from the Base class.

index.js

        var MyClass = require('./MyClass'),
            myObj;

        myObj = new MyClass();
        //...

        myObj.foo('blah'); // this will call foo in MyClass and on its parent class



<a id="@properties"></a>
##**@properties**: {Object} properties
>Aplicable to **Class**, **Object**

>Since **v0.0.1**

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

>Since **v0.0.1**

This annotation allows to define or specify which traits will be part of the current mix. A **Trait** is an special case of
class where you define only behavior that will be shared among other classes or even other Traits.

_Traits_ are Composable Units of Behaviour (You can read more from [this paper](http://scg.unibe.ch/archive/papers/Scha03aTraits.pdf)).
Basically, a Trait is a Class, let's say a special type of Class, that has only behaviour (methods) and no state. 

A Trait can be composed with other Traits. In Cocktail you cannot extend from a Trait or a Trait cannot extend from other class.

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

>Since **v0.0.1**

Talents are very similar to Traits, in fact a Trait can be applied as a Talent in Cocktail.
The main difference is a Talent can be applied to an _instance_. 
So we can define a Talent as a _Dynamically Composable Unit of Reuse_ (you can read more from [this paper](http://scg.unibe.ch/archive/papers/Ress11a-Talents.pdf)).

Talents share the same design principles that Traits and they have the same mechanisms (excludes and alias) to avoid method collision as in Traits.

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

>Since **v0.0.1**

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


