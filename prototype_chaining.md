# Prototype Chaining

## Problem

Duplication of object's members. Suppose that we want to have two or more objects with the same set of properties:

```
var initial = { property: "property value" };
```

There are two main strategies for solving this issue - each of them has its own semantics. So, which one to choose does depend on the specificity of the particular situation.

## Solution 1. Make deep copy of the object

```
var copy = extend({}, initial); // pseudo code, but this exact method could be found in JQuery
```

In this case we have two objects with two completely different lifes - any changes of the first one will not affect the second and vice versa.

## Solution 2. Use object as a prototype for a new one

```
var inherit = Object.create(initial); // initial will be used as a prototype for a newly created object
```

In this case those objects are bound - specifically, the object `inherit` is bound to `original`.

* In case of failed property lookup on `inherit` property set of the `initial` will be taken into account as a fallback.
* The `inherit` object could effectively "override" properties of the `initial` - if any property will be redefined in `inherit`, there will be no any fallback lookups on `initial` for that specific property.
* Any changes applied to the `original` object will effectively reflect to the `inherit` object - due to the fact of an existance of the fallback prototyping behavior.
