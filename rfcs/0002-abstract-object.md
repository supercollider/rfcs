- Title: Abstract Object
- Date proposed: 2020-12-19
- RFC PR: https://github.com/supercollider/rfcs/pull/0000 **update this number after RFC PR has been filed**


# Summary

An object superclass with minimal interface is added which opens a number of new features for sclang. This requires a small, but significant change of the class kernel, adding a class  `AbstractObject` as a superclass of  `Object`.

# Motivation

This suggested change makes the class library both more dynamic and flexible.
Since many years, this has been part of the Ruby programming language, whose class stucture sclang is partly modeled after.

Because of its minimal interface, we can use it as a superclass for a broad variety of classes:

- **Delegators** that receive method calls and forward them to a wrapped object
  - add instance variables to any wrapped object at runtime (e.g. a dictionary that holds metadata)
  - override methods at runtime (e.g. a logger that records all methods that were called)
  - reinterpret standard operations (e.g. a lift operator that lets us call methods on all objects in a collection at once)
  - simple and safe dependency mechanisms (no need for a central repository of dependants in the object class)
- **Prototype objects** whose methods are entries in a dictionary
  - they are equivalent to any other object and thus fully integrate (any method can be defined dynamically, not only those with new names)
  - current prototype objects using dictionaries are unsafe (a class extension to any class above Dictionary may break code)
- **Algebraic objects** and other features
  - lazy objects that construct a call tree instead of immediately executing the received calls.
  - method calls where each argument is informed that it will be passed to a given object
  - pluggable type systems, with a type signature for any method.


## It helps to solve some existing problems
- helps to reduce namespace pollution
- helps to solve the problem of combinatory explosion for combined features
- helps to avoid premature optimization (using classes and methods instead of functions), due to the lack of fully integrated prototype objects

## Method forwarding
Imagine a wrapper object which by default forwards calls to its wrapped object. If such a call forwarding is very cheap, a wrapper can be widely used in place of the  object it holds. This has several consequences, for example this makes it often unnecessary to modify the class library. Also, depending on how cheap the call forwarding is, several layers of wrappers can be added on to add functionality.

Helper methods can make this syntactically light. For example, the Neutral Quark has a method `lift`, which returns the receiver wrapped in a delegator that lifts all function calls to its elements:
```supercollider
a = [ "these", "are", "all", "mere", "words" ].lift;
a + "letters" //  Lift([ "these letters", "are letters", "all letters", "mere letters", "words letters" ])
```




# Preliminary work
An experimental implementation of a wide range of possible subclasses is to be found in the 'Neutral' Quark. It writes a class extansion file that overrides all methods that are not necessary. See: https://github.com/telephon/Neutral

Ruby has set an example with its class `BasicObject`
https://ruby-doc.org/core-2.7.2/BasicObject.html


# Specification

`AbstractObject` is a new class that is the superclass of  `Object`. The superclass of `AbstractObject` is `nil`. `AbstractObject` implements a minimal subset of the current `Object`  interface. By default, any class will still be subclass of `Object`, unless specified otherwise. Just like `Object`, the class of `AbstractObject`, which is called `Meta_AbstractObject`, has the superclass `Class`.


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

The additional class needs to be explained and understood. Currently, I can see no technical drawbacks.

If the class is used pervasively, the above changes in the primitives should be in place.

Regarding future delegator subclasses of `AbstractObject`, it is not clear how to make forwarded keyword arguments work. The same issue exists in the current object prototyping method and it probably needs to be solved separately.

# Unresolved Questions

1. One important question is whether this implementation has unintended consequences or complications in the backend, since it assumes `Object` to be the top end of the class hierarchy.

2. It should be carefully discussed which methods from  `Object` should be kept in `AbstractObject`. Some of this is a trade-off between fluent integration and flexibility. For example, introspection methods are expected to work also in subclasses of abstract object, but each of these method are fixed and their names are not available for delegation. For example, should `.isNil`  return the nil-ness of the wrapped object or just false? Should `.class` return the class of the wrapped object?

An example of what problems one can have in making an existing class, such as a database, compatible with the use of such delegators is here https://bugs.ruby-lang.org/attachments/7943

In general, introspection and bookkeeping methods should be kept. They can still be overridden in subclasses if necessary and possible. Some of these methods (e.g. `gcDumpGrey`) are necessary for the system to work, they need to be kept to avoid larger modifications of the backend.

There are boundary cases, for which we should find a general and clear reasoning: e.g.  `isKindOf` or `deepCopy`. The problem is not so much what to do, but how to communicate clearly the intuition behind it.

Both class methods `*new` and `*newCopyArgs` are implemented by `AbstractObject`. Should the classvars should be better kept in `Object`?
```
classvar <dependantsDictionary, currentEnvironment, topEnvironment, <uniqueMethods;
```


# Alternative Implementations

There may be an alternative to adding a primitive for erasing (possibly multiple) entries in the method table for all instances of an object at compile time. Then, any class could erase all methods that are not needed.

It is unclear if this is a realistic option in the current method table layout.
