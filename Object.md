# Object in javaScript
  通过《Professional JavaScript for Web Developers 3rd Edition》,《you don't know js》,MDN 较全面的理解 js Object]]
## [Object in Professional JavaScript](./ProfessionalObject.md)
## [Object in you don't know js](https://github.com/getify/You-Dont-Know-JS/blob/master/this%20%26%20object%20prototypes/README.md)
## [Object in mdn](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object)
## The Object constructor creates an object wrapper.(wrapper 包装器）
```js
// Object initialiser or literal
{ [ nameValuePair1[, nameValuePair2[, ...nameValuePairN] ] ] }

// Called as a constructor
new Object([value])
```
### Parameters
nameValuePair1, nameValuePair2, ... nameValuePairN
    Pairs of names (strings) and values (any value) where the name is separated from the value by a colon.
value
    Any value. 
### Description
The Object constructor creates an object wrapper for the given value. If the value is null or undefined, it will create and return an empty object, otherwise, it will return an object of a Type that corresponds to the given value. If the value is an object already, it will return the value.

When called in a non-constructor context, Object behaves identically to new Object().

See also the object initializer / literal syntax.  
### Properties of the Object constructor
Object.length
    Has a value of 1.
Object.prototype
    Allows the addition of properties to all objects of type Object. 
### Methods of the Object constructor
#### Object.assign()
    Copies the values of all enumerable own properties from one or more source objects to a target object.
#### Object.create()
    Creates a new object with the specified prototype object and properties.
#### Object.defineProperty()
    Adds the named property described by a given descriptor to an object.
#### Object.defineProperties()
    Adds the named properties described by the given descriptors to an object.
#### Object.entries()
    Returns an array containing all of the [key, value] pairs of a given object's own enumerable string properties.
#### Object.freeze()
    Freezes an object: other code can't delete or change any properties.
#### Object.fromEntries()
    Returns a new object from an iterable of key-value pairs (reverses Object.entries).
#### Object.getOwnPropertyDescriptor()
    Returns a property descriptor for a named property on an object.
#### Object.getOwnPropertyDescriptors()
    Returns an object containing all own property descriptors for an object.
#### Object.getOwnPropertyNames()
    Returns an array containing the names of all of the given object's own enumerable and non-enumerable properties.
#### Object.getOwnPropertySymbols()
    Returns an array of all symbol properties found directly upon a given object.
#### Object.getPrototypeOf()
    Returns the prototype of the specified object.
#### Object.is()
    Compares if two values are the same value. Equates all NaN values (which differs from both Abstract Equality Comparison and Strict Equality Comparison).
#### Object.isExtensible()
    Determines if extending of an object is allowed.
#### Object.isFrozen()
    Determines if an object was frozen.
#### Object.isSealed()
    Determines if an object is sealed.
#### Object.keys()
    Returns an array containing the names of all of the given object's own enumerable string properties.
#### Object.preventExtensions()
    Prevents any extensions of an object.
#### Object.seal()
    Prevents other code from deleting properties of an object.
#### Object.setPrototypeOf()
    Sets the prototype (i.e., the internal [[Prototype]] property).
#### Object.values()
    Returns an array containing the values that correspond to all of a given object's own enumerable string properties. 
    Object instances and Object prototype object
### Object instances and Object prototype object

All objects in JavaScript are descended from Object; all objects inherit methods and properties from Object.prototype, although they may be overridden. For example, other constructors' prototypes override the constructor property and provide their own toString() methods. Changes to the Object prototype object are propagated to all objects unless the properties and methods subject to those changes are overridden further along the prototype chain.
### Properties
#### Object.prototype.constructor
    Specifies the function that creates an object's prototype.
#### Object.prototype.__proto__
    Points to the object which was used as prototype when the object was instantiated.
#### Object.prototype.__noSuchMethod__
    Allows a function to be defined that will be executed when an undefined object member is called as a method.
### Methods


#### Object.prototype.__defineGetter__()
    Associates a function with a property that, when accessed, executes that function and returns its return value.
#### Object.prototype.__defineSetter__()
    Associates a function with a property that, when set, executes that function which modifies the property.
#### Object.prototype.__lookupGetter__()
    Returns the function associated with the specified property by the __defineGetter__() method.
#### Object.prototype.__lookupSetter__()
    Returns the function associated with the specified property by the __defineSetter__() method.
#### Object.prototype.hasOwnProperty()
    Returns a boolean indicating whether an object contains the specified property as a direct property of that object and not inherited through the prototype chain.
#### Object.prototype.isPrototypeOf()
    Returns a boolean indicating whether the object this method is called upon is in the prototype chain of the specified object.
#### Object.prototype.propertyIsEnumerable()
    Returns a boolean indicating if the internal ECMAScript [[Enumerable]] attribute is set.
#### Object.prototype.toSource()
    Returns string containing the source of an object literal representing the object that this method is called upon; you can use this value to create a new object.
#### Object.prototype.toLocaleString()
    Calls toString().
#### Object.prototype.toString()
    Returns a string representation of the object.
#### Object.prototype.unwatch()
    Removes a watchpoint from a property of the object.
#### Object.prototype.valueOf()
    Returns the primitive value of the specified object.
#### Object.prototype.watch()
    Adds a watchpoint to a property of the object.

### Deleting a property from an object


There isn't any method in an Object itself to delete its own properties (e.g. like Map.prototype.delete()). To do so one has to use the delete operator.
