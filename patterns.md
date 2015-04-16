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

## Function Class pattern

This pattern is very similar to the Object Decorator pattern, but there is a single difference though - the function will create an object within its scope instead of decorating an existing one:

```
function Car(location, speed) {
  var car = {}
  car.location = location;
  car.speed = speed;
  car.move = function() {
    car.location += car.speed;
  }
  
  return car;
}
```

Such a function that creates an object is called a `class factory function`. It is a good practice to give it a name that is starting from a capital letter.

## Prototypal Class pattern

The main goal of this pattern is to provide a single source of methods (functions) for all created instances of the class - and solve this task via prototypal fallback mechanics for the failed property lookups.

Consider the following class factory function:

```
function Car(location, speed) {
  var car = {};
  car.location = location;
  car.speed = speed;
  car.move = function() {
    car.location += car.speed;
  };
  
  return car;
}
```

Here we can note that the `move` function object will be created with each produced object - even taken into account the fact that the logic of this method remains the same for each instance of the class. In this way we could improve the situation in a following way:

```
function Car(location, speed) {
  var car = {};
  car.location = location;
  car.speed = speed;
  car.move = move;
  
  return car;
}

var move = function() {         // remember that compiler effectively moves variable's declaration (not the assignment though) 
  this.location += this.speed;  // at the top of the scope
};
```

Here the problem of function object's copying is solved, but we have another one - there is no evidence that `move` function belongs to the Car object. We could solve this issue using a prototypal fallback lookup mechanics as represented below (please, note that the main goal of this approach is to prevent storing twice the same objects - i.e., function objects. That's why there is no point to move `Car`'s properties to the prototype object):

```
function Car(location, speed) {
  var car = Object.create(Car.methods);
  car.location = location;
  car.speed = speed;
  
  return car;
}

Car.methods = {       // Note that Car is a function object - it is still a regular js object 
  move: function() {  // with a single specific feature - it can be invoked
    this.location += this.speed;
  } 
};
```

However, JS language has a predefined property that points to an already initialized empty object - a `prototype` property. It is a better practice to utilize this property as a mechanism for defining class methods by augmenting this `prototype` property object with a desired function set. It is worth to notice that this `prototype` property object is not really a prototype of the constructed object - at least till the point where we will define it as a prototype:

```
function Car(location, speed) {
  var car = Object.create(Car.prototype);
  car.location = location;
  car.speed = speed;
  
  return car;
}

Car.prototype = {       // Note that Car is a function object - it is still a regular js object 
  move: function() {    // with a single specific feature: it can be invoked
    this.location += this.speed;
  } 
};
```

Now this changes could look quite cosmetic. It is worth to point, however, that the pre-initialized `prototype` object is not quite empty - in fact, it has a property `constructor` which points back to the function object for which `prototype` property object has been created - i.e., the Car function object. Another words, the following evaluates to `true`:

```
Car.prototype.constructor === Car   // true
```

This fact could be very important in cases when `prototype` property object has been used as a constructed object's prototype. Here we can easily derive a constructor function form a created class instance:

```
var sweety = Car(0, 1);
sweety.constructor === Car; // true
```

Note here we have a request to the `sweety`'s prototype chain due to the fact that the `construtor` property is not defined for `sweety` itself. However, if we have used a `prototype` property object as a prototype for `sweety`, we know that it has a back-referencing `constructor` property being predefined.

### Operator `instanceof`

**`sweety instanceof Car`** - boolean operator that tests a presence of the `Car.prototype` object in a prototype chain of the `sweety` object

## Pseudoclassical pattern

This pattern is very similar to the Prototypal Class pattern. The main difference is the fact that the constructor function should be invoked with a keyword `new` in the client code: 

```
var sweety = new Car();
```

So what are the benifits of these modifications? Let's consider the `Car` constructor function realization before modifications being applied: 

```
function Car(location, speed) {
  var car = Object.create(Car.prototype); // boilerplate - object creation from a prototype
  car.location = location;
  car.speed = speed;
  
  return car; // boilerplate - return constructed object
}

Car.prototype = {       
  move: function() {   
    this.location += this.speed;
  } 
};
```

Here we can see the lines that will very quickly become a mundane work with a several more classes being defined - those lines are marked with a comments. Specifically, these lines are:
* `var car = Object.create(Car.prototype)` - new object creation from a prototype
* `return car` - created object returning

In order to avoid such kind of duplication JS compiler provides a special mechanism that will be launched any time when a function object will be invoked with a keyword `new`. In this case the following function:
```
var sweety = Any();

function Any() {
  ... // actual body of the function
}
```

will be augmented with the following very-first and very-last lines:

```
var sweety = new Any();

function Any() {
  this = Object.create(Any.prototype);
  ... // actual body of the function
  return this;
}
```

### Constructor function refactoring

Taking the above fact into account, we can rewrite the constructor function in the following way:

```
function Car(location, speed) {
  this.location = location;
  this.speed = speed;
}

Car.prototype = {       // Note that Car is a function object - it is still a regular js object 
  move: function() {    // with a single specific feature: it can be invoked
    this.location += this.speed;
  } 
};
```

### Additional notes

* Now it is implied that this constructor function should be invoked with a keyword `new` for reaching a proper effect.
* Class declaration now could be divided into two main logical parts:
  * common similar members (in our case those are represented by methods only) are defined within a prototypal part
  * differences are definied in constructor function itself - commonly it should deal with the parameters that were passed in the constructor
