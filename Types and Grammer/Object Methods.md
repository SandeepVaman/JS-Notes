# JS Object methods

## Array.from()
This is a static method that creates an array based on another array or string. You can also pass a map callback function as an argument to further shape the data in the new array. Honestly, I'm not too sure why someone would use this over the .map () method.
### Example
Create an array from a string.

```js
const newArray = Array.from('hello');
// newArray will be equal to ['h', 'e', 'l', 'l', 'o']
```
Create an array that has double the value for each item in another array.

```js
const doubleValues = Array.from([2,4,6], number => number * 2);
```

## Object.values()

Return an array if the values of an object.

### Example
```js
const icecreamColors = {
  chocolate: 'brows,
  vanilla: 'white',
  strawberry: 'red',
}

const colors = Object.values(icecreamColors);

//colors will be equal to ["brown", "white", "red"]
```

## Object.keys()

Reurn an array of the keys of an object.

###Example
```js
const icecreamColors = {
  chocolate: 'brown',
  vanilla: 'white',
  strawberry: 'red',
}

const types = Object.keys(icecreamColors);
// types will be equal to ["chocolate", "vanilla", "strawberry"]
```

Object.entries()
Creates an array which contains arrays of key/value pairs of an object.

### Example
```js
const weather = {
  rain: 0,
  temperature: 24,
  humidity: 33,
}

const entries = Object.entries(weather);
// entries will be equal to
// [['rain', 0], ['temperature', 24], ['humidity', 33]]
```
## Object spread
Spreading an object allows for addition of new properties and values to an object without mutations(i.e a new object is created) and it can also be used to combine multiple objects together. It should be noted that spreading objects does not do nesting copying.

### Example

Add a new object property and value without mutating the original object.

```js
const spreadableObject = {
  name: 'Robert',
  phone: 'iPhone'
};

const newObject = {
  ...spreadableObject,
  carModel: 'Volkswagen'
}
// newObject will be equal to
// { carModel: 'Volkswagen', name: 'Robert', phone: 'iPhone' }
```
## Function Rest
Function can use the rest parameter syn tax to accept any number of arguments as an array.

### Example

Display the array of passed arguments.

```js
function displayArgumentsArray(...theArguments) {
  console.log(theArguments);
}

displayArgumentsArray('hi', 'there', 'bud');
// Will print ['hi', 'there', 'bud']
```
# Object.freeze()
Prevents you from modifying existing object properties or adding new properties and values to an object. It's often what people think const does, however const allows you to modify an object.

### Example

Freeze an object to prevent the name property from being changed.

```js
const frozenObject = {
  name: 'Robert'
}

Object.freeze(frozenObject);

frozenObject.name = 'Henry';
// frozenObject will be equal to { name: 'Robert' }
```
# Object.seal()
The Object.seal() method seals an object, preventing new properties from being added to it and marking all existing properties as non-configurable. Values of present properties can still be changed as long as they are writable.

### Example 
Seal an object to prevent the wearsWatch property from being added.

```js
const sealedObject = {
  name: 'Robert'
}

Object.seal(sealedObject);

sealedObject.name = 'Bob';
sealedObject.wearsWatch = true;
// sealedObject will be equal to { name: 'Bob' }
```
## Object.assign()
The Object.assign() method is used to copy the values of all enumerable own properties from one or more source objects to a target object. It will return the target object.
###Example
Combine two objects into one.
```js
const object1 = {
  a: 1,
  b: 2,
  c: 3
};

const object2 = Object.assign({c: 4, d: 5}, object1);

console.log(object2.c, object2.d);
// expected output: 3 5

```

