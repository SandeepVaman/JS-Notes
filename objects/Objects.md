# Objects

## Syntax
Objects come in two forms: the declarative (literal) form, and the constructed form.

The literal syntax for an object looks like this:

```js
var myObj = {
  key:value
}
```

The constructed form looks like this.
```js
var myObj = new Object();
myObj.key = value;
```
The difference between these two types is in literal form we can add one or more properties at the same time whereas in constructed form we have to add the properties one by one.

## Type
Objects are one of the 6 primary types in JS.
string, number, boolean, null and undefined are not themselves objects. null is sometimes refered to as object, but it is a syntact error.

**It's a common mis-statement that "everything in JavaScript is an object". This is clearly not true.**

By contrast, there are a few special object sub-types such as arrays and functions, which we can refer to as complex primitives.

## Built in Objects
There are several other object sub-types, usually referred to as built-in objects. For some of them, their names seem to imply they are directly related to their simple primitives counter-parts, but in fact, their relationship is more complicated.

* String
* Number
* Boolean
* Object
* Function
* Array
* Date
* RegExp
* Error

Each of these built-in functions can be used as a constructor, with the result being a newly constructed object of the sub-type in question.

```js
var strPrimitive = "I am a string";
typeof strPrimitive;							// "string"
strPrimitive instanceof String;					// false

var strObject = new String( "I am a string" );
typeof strObject; 								// "object"
strObject instanceof String;					// true

// inspect the object sub-type
Object.prototype.toString.call( strObject );	// [object String]
```

The primitive value "I am a string" is not an object, it's a primitive literal and immutable value. To perform operations on it, such as checking its length, accessing its individual character contents, etc, a String object is required.

Luckily, the language automatically coerces a "string" primitive to a String object when necessary, which means you almost never need to explicitly create the Object form.

## Contents
As mentioned earlier, the contents of an object consist of values (any type) stored at specifically named locations, which we call properties.

```js
var myObject = {
	a: 2
};

myObject.a;		// 2

myObject["a"];	// 2
```
To access the property we have to use `.` oprator ot `[]` operator. The .a syntax is usually referred to as "property" access, whereas the ["a"] syntax is usually referred to as "key" access. [] operator is identifier compatible.

In objects, property names are always strings.
Numbers which are usually used as array indexes, will converted into string value.
```js
var myObject = { };

myObject[true] = "foo";
myObject[3] = "bar";
myObject[myObject] = "baz";

myObject["true"];				// "foo"
myObject["3"];					// "bar"
myObject["[object Object]"];	// "baz"
```
## Computed Property Names
The myObject[..] property access syntax we just described is useful if you need to use a computed expression value as the key name, like myObject[prefix + name].
ES6 adds computed property names, where you can specify an expression, surrounded by a [ ] pair, in the key-name position of an object-literal declaration:
```js
var prefix = "foo";

var myObject = {
	[prefix + "bar"]: "hello",
	[prefix + "baz"]: "world"
};

myObject["foobar"]; // hello
myObject["foobaz"]; // world
```
## Property vs Method
"function" and "method" are interchangeable in JavaScript.

## Arrays
Arrays also use the [ ] access form, they have slightly more structured organization for how and where values are stored .Arrays assume numeric indexing, which means that values are stored in locations, usually called indices, at non-negative integers, such as 0 and 42.

```js
var myArray = [ "foo", 42, "bar" ];
myArray.length;		// 3

myArray[0];			// "foo"

myArray[2];		// "bar"
```
Arrays are objects, so even though each index is a positive integer, you can also add properties onto the array:

```js
var myArray = [ "foo", 42, "bar" ];

myArray.baz = "baz";

myArray.length;	// 3

myArray.baz;	// "baz"
```
Adding named poperty does not change the reported `length` of the array.

**Be carefull**  If you try to add a property to an array, but the property name looks like a number, it will end up instead as a numeric index (thus modifying the array contents):

## Duplicating Objects
There are two kinds of copying Shallow copying and deep copying. 
Shallow as name suggests copies only primitive values, but for refrences it again keeps refrences.
Deep copy should copy and create compeltely new properties, but it fails for circular dependency.

```js
function anotherFunction() { /*..*/ }

var anotherObject = {
	c: true
};

var anotherArray = [];

var myObject = {
	a: 2,
	b: anotherObject,	// reference, not a copy!
	c: anotherArray,	// another reference!
	d: anotherFunction
};

anotherArray.push( anotherObject, myObject ); //Added circular refrence.
```
One hack to copy the object is JSON.stringifying the object and parsing it.
```js
var newObj = JSON.parse( JSON.stringify( someObj ) );
```
But the object should be JSON safe.

At the same time, a shallow copy is fairly understandable and has far less issues, so ES6 has now defined Object.assign(..) for this task. Object.assign(..) takes a target object as its first parameter, and one or more source objects as its subsequent parameters. It iterates over all the enumerable (see below), owned keys (immediately present) on the source object(s) and copies them (via = assignment only) to target. It also, helpfully, returns target, as you can see below:

```js
var newObj = Object.assign( {}, myObject );

newObj.a;						// 2
newObj.b === anotherObject;		// true
newObj.c === anotherArray;		// true
newObj.d === anotherFunction;	// true
```
**The duplication that occurs for Object.assign(..) however is purely = style assignment, so any special characteristics of a property (like writable) on a source object are not preserved on the target object.**

## Property Descriptors

```js
Object.getOwnPropertyDescriptor( Object, "property" );
```
Consider this code:

```js
var myObject = {
	a: 2
};

Object.getOwnPropertyDescriptor( myObject, "a" );
// {
//    value: 2,
//    writable: true,
//    enumerable: true,
//    configurable: true
// }
```
Object.defineProperty(..) is used to define a property in objects.

For example:
```js
var myObject = {};

Object.defineProperty( myObject, "a", {
	value: 2,
	writable: true,
	configurable: true,
	enumerable: true
} );

myObject.a; // 2
```

### Writable
The ability for you to change the value of a property is controlled by writable.

```js
var myObject = {};

Object.defineProperty( myObject, "a", {
	value: 2,
	writable: false, // not writable!
	configurable: true,
	enumerable: true
} );

myObject.a = 3;

myObject.a; // 2
```

modification of the value silently failed. If we try in strict mode, we get an error.

### Configurable
As long as a property is currently configurable, we can modify its descriptor definition, using the same defineProperty(..) utility.
```js
var myObject = {
	a: 2
};

myObject.a = 3;
myObject.a;					// 3

Object.defineProperty( myObject, "a", {
	value: 4,
	writable: true,
	configurable: false,	// not configurable!
	enumerable: true
} );

myObject.a;					// 4
myObject.a = 5;
myObject.a;					// 5

Object.defineProperty( myObject, "a", {
	value: 6,
	writable: true,
	configurable: true,
	enumerable: true
} ); // TypeError
```
The final defineProperty(..) call results in a TypeError, regardless of strict mode, if you attempt to change the descriptor definition of a non-configurable property.

Be careful: as you can see, changing configurable to false is a one-way action, and cannot be undone!

Another thing configurable:false prevents is the ability to use the delete operator to remove an existing property.

```js
var myObject = {
	a: 2
};

myObject.a;				// 2
delete myObject.a;
myObject.a;				// undefined

Object.defineProperty( myObject, "a", {
	value: 2,
	writable: true,
	configurable: false,
	enumerable: true
} );

myObject.a;				// 2
delete myObject.a;
myObject.a;				// 2
```
As you can see, the last delete call failed (silently) because we made the a property non-configurable.
delete is only used to remove object properties (which can be removed) directly from the object in question. If an object property is the last remaining reference to some object/function, and you delete it, that removes the reference and now that unreferenced object/function can be garbage collected. But, it is not proper to think of delete as a tool to free up allocated memory as it does in other languages (like C/C++). delete is just an object property removal operation -- nothing more.

### Enumerable
he name probably makes it obvious, but this characteristic controls if a property will show up in certain object-property enumerations, such as the for..in loop. Set to false to keep it from showing up in such enumerations, even though it's still completely accessible. Set to true to keep it present.

## Immutability
It is sometimes desired to make properties or objects that cannot be changed (either by accident or intentionally). ES5 adds support for handling that in a variety of different nuanced ways.

It's important to note that all of these approaches create shallow immutability. That is, they affect only the object and its direct property characteristics. If an object has a reference to another object (array, object, function, etc), the contents of that object are not affected, and remain mutable.

```js
myImmutableObject.foo; // [1,2,3]
myImmutableObject.foo.push( 4 );
myImmutableObject.foo; // [1,2,3,4]
```
### Object Constant
By combining writable:false and configurable:false, you can essentially create a constant (cannot be changed, redefined or deleted) as an object property, like:
```js
var myObject = {};

Object.defineProperty( myObject, "FAVORITE_NUMBER", {
	value: 42,
	writable: false,
	configurable: false
} );
```
### Prevent Extensions
If you want to prevent an object from having new properties added to it, but otherwise leave the rest of the object's properties alone, call Object.preventExtensions(..):
```js
var myObject = {
	a: 2
};

Object.preventExtensions( myObject );

myObject.b = 3;
myObject.b; // undefined
```
In non-strict mode, the creation of b fails silently. In strict mode, it throws a TypeError.


### Seal
Object.seal(..) creates a "sealed" object, which means it takes an existing object and essentially calls Object.preventExtensions(..) on it, but also marks all its existing properties as configurable:false.

### Freeze
Object.freeze(..) creates a frozen object, which means it takes an existing object and essentially calls Object.seal(..) on it, but it also marks all "data accessor" properties as writable:false, so that their values cannot be changed.
