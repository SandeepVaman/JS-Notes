# this keyword
`this` is the most confused keyword in javascript.It's a special identifier keyword that's automatically defined in the scope of every function.

## Why `this`?

```js
function identify(){
  return this.name.toUpperCase();
}

function speak(){
  var greeting = "Hello, I'm "+ identify.call(this);
  console.log(greeting);
}

var me = {
  name: "Sandeep"
};

var you = {
  name: "Reader"
};

identify.call(me); //SANDEEP
identify.call(you); // READER

speak.call(me); // Hello, I'm SANDEEP
speak.call(you); // Hello, I'm READER

```
This code snippet allows the `identify()` and `speak()` functions to be re-used against multiple constext(me and you) objects, rather than needing a seperate version of the functiion for each object.

Instead of relying on `this`, you could have explicitely passed in a context object to bpoth `identify()` and `speak()`.

```js
function identify(context){
  return context.name.toUpperCase();
}

function speak(context) {
  var greeting = "Hello, I'm" + identify(context);
  console.log(greeting);
}

identify(you); // READER
speak(me); // Hello, I'm SANDEEP
}
```
However, the this mechanism provides a more elegant way of implicitly "passing along" an object reference, leading to cleaner API design and easier re-use.

## Confusions

### Itself
The first common temptation is to assume this refers to the function itself. That's a reasonable grammatical inference, at least.

Why would you want to refer to a function from inside itself? The most common reasons would be things like recursion (calling a function from inside itself) or having an event handler that can unbind itself when it's first called.

Consider the following code, where we attempt to track how many times a function (foo) was called:

```js
  function foo(num){
    console.log("foo:" + num);
    
    //keep track of how many times `foo` is called.
    this.count++;
  }
  
  foo.count = 0 // trying to maintain a state for the object.
  
  var i;
  
  for(i=0;i<5;i++){
    foo(i);
  }
  // foo:0
  // foo:1
  // foo:2
  // foo:3
  // foo:4
console.log( foo.count ); // 0 -- WTF?
```
foo.count is still 0, even though the four console.log statements clearly indicate foo(..) was in fact called four times. The frustration stems from a too literal interpretation of what this (in this.count++) means.


When the code executes foo.count = 0, indeed it's adding a property count to the function object foo. But for the this.count reference inside of the function, this is not in fact pointing at all to that function object, and so even though the property names are the same, the root objects are different, and confusion ensues.

Here, the function is called from the global context, `this.count` is undefined initially, when the function increments the count each time, the NaN value is produced.
`console.log(count)` would result in NaN.

Some developers prefer this hack to resolve this problem.
```js
function foo(num) {
	console.log( "foo: " + num );

	// keep track of how many times `foo` is called
	data.count++;
}
var data = {
	count: 0
};
for(i=0;i<5;i++){
    foo(i);
  }
  // foo:0
  // foo:1
  // foo:2
  // foo:3
  // foo:4
  
 console.log( data.count ); // 5
```
But this is just a hack, and clearly shows this developer lacks knowledge on `this`.
To reference a function object from inside itself, this by itself will typically be insufficient. You generally need a reference to the function object via a lexical identifier (variable) that points at it.
```js
function foo(){
  foo.count = 4; //`foo` referes to itself.
}

setTimeout( function(){
    // anonymous function (no name), cannot
  	// refer to itself
    
}, 10)
```
In the first function, called a "named function", foo is a reference that can be used to refer to the function from inside itself.
But in the second example, the function callback passed to setTimeout(..) has no name identifier (so called an "anonymous function"), so there's no proper way to refer to the function object itself.

Note:  The old-school but now deprecated and frowned-upon arguments.callee reference inside a function also points to the function object of the currently executing function. This reference is typically the only way to access an anonymous function's object from inside itself.

```js
function foo(num) {
	console.log( "foo: " + num );

	// keep track of how many times `foo` is called
	foo.count++;
}

foo.count = 0;

var i;

for (i=0; i<5; i++) {
		foo( i );
}
// foo: 0
// foo: 1
// foo: 2
// foo: 3
// foo: 4

// how many times was `foo` called?
console.log( foo.count ); // 5
```

Yet another way of approaching the issue is to force this to actually point at the foo function object:
```js
function foo(num) {
	console.log( "foo: " + num );

	// keep track of how many times `foo` is called
	// Note: `this` IS actually `foo` now, based on
	// how `foo` is called (see below)
	this.count++;
}

foo.count = 0;

var i;

for (i=0; i<5; i++) {
		foo.call(foo, i);
}
// foo: 0
// foo: 1
// foo: 2
// foo: 3
// foo: 4

// how many times was `foo` called?
console.log( foo.count ); // 5

```

The next most common misconception about the meaning of this is that it somehow refers to the function's scope. It's a tricky question, because in one sense there is some truth, but in the other sense, it's quite misguided
Consider code which attempts (and fails!) to cross over the boundary and use this to implicitly refer to a function's lexical scope:
```js
function foo() {
	var a = 2;
	this.bar();
}

function bar() {
	console.log( this.a );
}

foo(); //undefined
```
Firstly, an attempt is made to reference the bar() function via this.bar(). It is almost certainly an accident that it works, but we'll explain the how of that shortly. The most natural way to have invoked bar() would have been to omit the leading this. and just make a lexical reference to the identifier.

However, the developer who writes such code is attempting to use this to create a bridge between the lexical scopes of foo() and bar(), so that bar() has access to the variable a in the inner scope of foo(). **No such bridge is possible**. You cannot use a this reference to look something up in a lexical scope. It is not possible.
