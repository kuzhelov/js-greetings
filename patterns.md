# Patterns

## Object Decorator pattern

### Problem

needness to add specific properties and capabilities to the object in order to supply it with a ploymorhic behavior for a specific interface. For example, suppose that we have an object `sweety` and also would like to augment it with a car like behavior. 

### Solution

In this case we could define a decorator method:

```
function carlike(object, location, speed) {
  object.location = location;
  object.speed = speed;
  object.move = function() {
    object.location += speed;
  }
  
  return object;
}
```

and use it in a following way afterwards:

```
var sweety = {};
sweety = carlike(sweety, 0, 1);

sweety.move();
```

### Additional notes

* It is a common practice to use adjective names for decorator methods.
* It should be noted that the new function object `move` will be created with each instance being passed to the `carlike` function. In order to avoid the copying and using the single function instance instead the following technique could be used (it is worth to notice, however, that this technique could lead to suffcient decrease of maintainability):
```
var move = function() {
  this.location += this.speed;
}

function carlike(...) {
  ...
  object.move = move;
}
```
