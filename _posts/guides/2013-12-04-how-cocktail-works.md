---
layout: guide
title: Understanding the Mix process.
published: true
category: guides
tags: [Intermediate]
comments: true
---
 
This guide is intended to describe a bit deeper how the **cocktail.mix** process works.

#Inside cocktail.mix()

The main flow inside the mix process is basically splitted into 3 phases: 

- Determine what will be mixed and apply defaults.
- Configure and enqueue the processors for each annotation.
- Execute the processors.

## Determine What Is Being Mixed

You can use the mix process with one or two parameters. In the first scenario, giving a single parameter, there are some internal execution that inspect the current object looking for the pseudo-annotation **'@as'** to decide if creating a default constructor is needed or if we will operate over a module definition. That creates a new **subject** and uses the given parameter as the **options** object.

When using two parameters, or after the subject is created as we described, the subject remains untoched in this phase and then the options object is analysed to apply default annotations - such is the case of **'@merge'**.

The default value applied in the options object is only one and it is applied in case there is no _'@merge'_ annotation specified, since cocktail needs a default _strategy to mix subject and options_. The default value for **'@merge'** is **single**.

## Configure Processors

Once the options object is normalized with default values, it goes through a process to parse the annotations. Each key that is specified in the form of an annotation is matched with the list of current Processors. If the processor exists, a new instance is created and the **setParameter** method is called with the given arguments in the annotation and the instance is added into the execution queue.

The **retain** parameter in the processor determines if the key should be removed or not from the options object once it has been processed. The default behavior is to remove all processed annotations.

## Execute the Processors

Finally, once all annotations have been processed, they have to be executed in a given order. Each processor has a property named **prioritiy**. The execution queue is sorted based on that priority and then the subject and options are passed into each Processor's **process** method.

