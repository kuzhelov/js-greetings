# This Keyword

* Generally speaking, `this` keyword represents an object from each the function has been called from. That is why it is not possible to say for sure which object `this` is bound to given only the body of the function.
* Only the call time defines to which object `this` will be bound.
* If there is no parameter supplied for `this` it will be bound to the `global` object.
* Invocation of function with a keyword `new` will result in binding of `this` to a brand new object:
```
new object1.fn();

function fn() {
  log(this); // {} - irrelevantly to what object1 is.
}
```

## Invocation by the mean of the specific object

There are several ways to invoke the function by the means of the specific object:

* **`object`.`function`()** - ***left-to-the-dot rule*** - could be used if `function` is a defined property of `object`
* **`object`\['`function`'\]()** - same as the above
* **`function`.call(`binding-for-this`, `first-arg`, `second-arg`..)** - using a `call` method on function object. In this way the first parameter that will be passed to invocation will be bound to `this`. Note that in the following example `this` will be bound to `object2` due to the fact that `call` overrides method access rules: 
```
object1.function.call(object2, ..)
```

## Problem of loosing parameters' bindings

**Problem**

Could be seen in the following code snippet:

`setTimeout(r.fn, 100)`

given the following way of implementing `setTimeot` (pseudocode):

```
function setTimeout(cb, interval) {
  wait(interval)
  cb()
}
```

Here the `fn` function will be called with `this` being bound to `global`. 

**Solution** 

Pass a completely new function object as a callback:

```
setTimeout(function() { r.fn() }, 100)
```
