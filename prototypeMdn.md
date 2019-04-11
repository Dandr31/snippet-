# Object.prototype 属性表示 Object 的原型对象。

Object.prototype 属性的属性特性：
writable 	true
enumerable 	false
configurable 	true

# Description

Nearly all objects in JavaScript are instances of Object; a typical object inherits properties (including methods) from Object.prototype, although these properties may be shadowed(遮蔽) (a.k.a. overridden 亦称为覆盖). However, an Object may be deliberately(adv.
故意地; 深思熟虑地; 从容不迫地;) created for which this is not true (e.g. by Object.create(null)), or it may be altered so that this is no longer true (e.g. with Object.setPrototypeOf).

Changes to the Object prototype object are seen by all objects through prototype chaining, unless the properties and methods subject to those changes are overridden further(adv 进一步) along the prototype chain. This provides a very powerful although potentially dangerous mechanism to override or extend object behavior.

# Properties

## Object.prototype.constructor
    Specifies the function that creates an object's prototype.
## Object.prototype.__proto__
    Points to the object which was used as prototype when the object was instantiated.
## Object.prototype.__noSuchMethod__
    Allows a function to be defined that will be executed when an undefined object member is called as a method.
    
# Methods
## Object.prototype.__defineGetter__()
    Associates a function with a property that, when accessed, executes that function and returns its return value.
## Object.prototype.__defineSetter__()
    Associates a function with a property that, when set, executes that function which modifies the property.
## Object.prototype.__lookupGetter__()
    Returns the function associated with the specified property by the __defineGetter__() method.
## Object.prototype.__lookupSetter__()
    Returns the function associated with the specified property by the __defineSetter__() method.
## Object.prototype.hasOwnProperty()
    Returns a boolean indicating whether an object contains the specified property as a direct property of that object and not inherited through the prototype chain.
## Object.prototype.isPrototypeOf()
    Returns a boolean indicating whether the object this method is called upon is in the prototype chain of the specified object.
## Object.prototype.propertyIsEnumerable()
    Returns a boolean indicating if the internal ECMAScript [[Enumerable]] attribute is set.
## Object.prototype.toSource()
    Returns string containing the source of an object literal representing the object that this method is called upon; you can use this value to create a new object.
## Object.prototype.toLocaleString()
    Calls toString().
## Object.prototype.toString()
    Returns a string representation of the object.
## Object.prototype.unwatch()
    Removes a watchpoint from a property of the object.
## Object.prototype.valueOf()
    Returns the primitive value of the specified object.
## Object.prototype.watch()
    Adds a watchpoint to a property of the object.   
    
# Examples


When altering the behavior of existing Object.prototype methods, consider injecting code by wrapping your extension before or after the existing logic. For example, this (untested) code will pre-conditionally execute custom logic before the built-in logic or someone else's extension is executed.

When a function is called, the arguments to the call are held in the array-like "variable" arguments. For example, in the call "myFn(a, b, c)", the arguments within myFn's body will contain 3 array-like elements corresponding to (a, b, c). When modifying prototypes with hooks, simply pass this & the arguments (the call state) to the current behavior by calling apply() on the function. This pattern can be used for any prototype, such as Node.prototype, Function.prototype, etc.
```js
var Person = function(name) {
    this.name = name;
    this.canTalk = true;
  };
  
  Person.prototype.greet = function() {
    if (this.canTalk) {
      console.log('Hi, I am ' + this.name);
    }
  };
  
  var Employee = function(name, title) {
    Person.call(this, name);
    this.title = title;
  };
  
  Employee.prototype = Object.create(Person.prototype);
  
  Employee.prototype.greet = function() {
    if (this.canTalk) {
      console.log('Hi, I am ' + this.name + ', the ' + this.title);
    }
  };
  
  var Customer = function(name) {
    Person.call(this, name);
  };
  
  Customer.prototype = Object.create(Person.prototype);
  
  var Mime = function(name) {
    Person.call(this, name);
    this.canTalk = false;
  };
  
  Mime.prototype = Object.create(Person.prototype);
  
  var bob = new Employee('Bob', 'Builder');
  var joe = new Customer('Joe');
  var rg = new Employee('Red Green', 'Handyman');
  var mike = new Customer('Mike');
  var mime = new Mime('Mime');
  
  bob.greet();
  // Hi, I am Bob, the Builder
  
  joe.greet();
  // Hi, I am Joe
  
  rg.greet();
  // Hi, I am Red Green, the Handyman
  
  mike.greet();
  // Hi, I am Mike
  
  mime.greet();
```
