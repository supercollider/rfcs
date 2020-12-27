- Title: Abstract Object
- Date proposed: 2020-12-19
- RFC PR: https://github.com/supercollider/rfcs/pull/0000 **update this number after RFC PR has been filed**


# Summary

An object superclass with minimal interface is added which opens a number of new features for sclang. This requires a small, but significant change of the class kernel, adding a class  `AbstractObject` as a superclass of  `Object`.

# Motivation

Adding an `AbstractObject` makes the class library both more dynamic and flexible.
Since many years, a similar class (`BasicObject`) has been part of the Ruby programming language, whose class stucture sclang is partly modeled after.

Because of its minimal interface, we can use it as a superclass for a broad variety of classes:

- **Delegators**, that receive method calls and forward them to a wrapped object, e.g.
  - add (pseudo-)instance variables to any wrapped object at runtime (e.g. a dictionary that holds metadata)
  - override methods at runtime (e.g. a logger that records all methods that were called)
  - objects can change interface depening on context (e.g. each environment can supply its own interface)
  - reinterpret standard operations (e.g. a lift operator that lets us call methods on all objects in a collection at once)
  - simple and safe dependency mechanisms (no need for a central repository of dependants in the object class)
- **Prototype objects**, whose only methods are entries in a dictionary, e.g.
  - they are equivalent to any other object and thus fully integrate (any method can be defined dynamically, not only those with new names)
  - solves the problem of current prototype objects using dictionaries, which are unsafe (a class extension to any class above Dictionary may break code)
- **Algebraic objects** and other features, e.g.
  - lazy objects that construct a call tree instead of immediately executing the received calls.
  - method calls where each argument is informed that it will be passed to a given object
  - pluggable type systems, with a type signature for any method


## It helps to solve some existing problems
- helps to reduce namespace pollution (not every feature needs a class)
- helps to solve the problem of combinatory explosion for combined features (one object can have different methods depending on context)
- helps to avoid premature optimization (using classes and methods instead of functions), due to the lack of fully integrated prototype objects

## Method forwarding
Imagine a wrapper object which by default forwards calls to its wrapped object. If such a call forwarding is very cheap, a wrapper can be widely used in place of the object it holds. This has several consequences, for example this makes it often unnecessary to modify the class library. Also, depending on how cheap the call forwarding is, several layers of wrappers can be added on to add functionality.

Helper methods can make this also syntactically light. For example, the Neutral Quark has a method `lift`, which returns the receiver wrapped in a delegator that lifts all function calls to its elements:
```supercollider
a = [ "these", "are", "all", "mere", "words" ].lift;
a + "letters" //  Lift([ "these letters", "are letters", "all letters", "mere letters", "words letters" ])
```




# Preliminary work
An experimental implementation of a wide range of possible subclasses is to be found in the 'Neutral' Quark. Because the `Object` interface is large, it writes a class extension file that overrides all methods that are not necessary with `doesNotUnderstand` (so it needs to be compiled twice). See: https://github.com/telephon/Neutral

Ruby has set an example with its class `BasicObject`
https://ruby-doc.org/core-2.7.2/BasicObject.html


# Specification

`AbstractObject` is a new class, a superclass of  `Object`. `AbstractObject` implements a minimal subset of the current `Object` interface (see _Unresolved Questions_ what "minimal" means). By default, any class will still be subclass of `Object`, unless specified otherwise. The only exception is `AbstractObject`, whose superclass is `nil`.  Just like `Object`, the class of `AbstractObject`, which is called `Meta_AbstractObject`, has the superclass `Class`.

The sclang code will look like this:

```supercollider
AbstractObject {

}

Object : AbstractObject {

}
```


| Current structure of class kernel | Suggested structure of class kernel |
| ----------- | ----------- |
| <image src="images/supercollider-class-structure.gif" width=400 alt="Diagram of the current structure of class kernel"> | <image src="images/supercollider-class-structure-with-abstract-object.gif" width=600 alt="Diagram of the current structure of class kernel"> |


Theoretically, one may also want to make the class `Meta_AbstractObject` be a subclass of a new class `AbstractClass`. This is not easy, because the interface of the class `Class` is important for introspection. But it is better to keep introspection methods anyhow (see below under Unresolved Questions).


## Suggested changes in primitives for optimization and integration

1. An optional, but desirable optimization is a primitive that forwards a method to another object (see comment under Motivation)
2. For a fully transparent integration in sclang, the implementation of the `if` operator would have to handle a fallback: https://github.com/supercollider/supercollider/issues/3567
3. Probably a more difficult one: object prototypes would become even better integrated if the keyword `this` could be assigned to the prototype object programmatically. The experimental implementation uses a class called `This` that can be used in such a way, but it is another special case to be avoided.

**This RFC is independent of these suggested changes.**

# Drawbacks

The additional class needs to be well explained and well understood. Currently, I can see no technical drawbacks.

If the class is used pervasively, the above changes in the **primitives** should be in place as to make its use computationally efficient.

Regarding future delegator subclasses of `AbstractObject`, it is not clear how to make forwarded **keyword arguments** work. The same issue exists in the current object prototyping method and it probably needs to be solved separately.

# Unresolved Questions
 (partly resolved, but open for discussion)

## Check for unintended consequences
One important question is whether this implementation has unintended consequences or complications in the backend, since it assumes `Object` to be the top end of the class hierarchy.

## Instance methods of Object
It should be carefully discussed which instance methods from  `Object` should be kept in `AbstractObject`. Some are needed in the backend.

Ruby follows an extremely lean approach, which leads to extra demands on classed that should interoperate with BasicObject. An example of what problems one can have in making an existing class, such as a database, compatible with the use of such delegators is here https://bugs.ruby-lang.org/attachments/7943

I have in mind a more balanced approach. In general, introspection and bookkeeping methods should be kept. They can still be overridden in subclasses if necessary and possible. 

Some of these methods (e.g. `gcDumpGrey`) are necessary for the system to work, they need to be kept to avoid larger modifications of the backend. Some of this is also a trade-off between fluent integration and flexibility. For example, introspection methods are expected to work also in subclasses of abstract object, but each of these method are fixed and their names are not available for delegation. Should `.isNil`  return the nil-ness of the wrapped object or just false? Should `.class` return the class of the wrapped object?

There are a number of boundary cases (e.g. `isKindOf` or `deepCopy`), for which we should find a general and clear reasoning. 

_I think, the problem is not so much what to do, but how to communicate clearly the intuition behind it._

## class methods and class variables
Both class methods `*new` and `*newCopyArgs` are implemented by `AbstractObject`. Should the classvars should be better kept in `Object`?
```
classvar <dependantsDictionary, currentEnvironment, topEnvironment, <uniqueMethods;
```


# Alternative Implementations

There may be an alternative to adding a primitive for erasing (possibly multiple) entries in the method table for all instances of an object at compile time. Then, any class could erase all methods that are not needed. This could be used to implement `AbstractObject` without adding it as a superclass to `Object`

It is unclear if this is a realistic option in the current method table layout.
