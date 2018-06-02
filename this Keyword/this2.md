## Call site
To understand this binding, we have to understand the call-site: the location in code where a function is called (not where it's declared). We must inspect the call-site to answer the question: what's this this a reference to?


We turn our attention now to how the call-site determines where this will point during the execution of a function.

You must inspect the call-site and determine which of 4 rules applies. We will first explain each of these 4 rules independently, and then we will illustrate their order of precedence, if multiple rules could apply to the call-site.

## 1. Default Binding

The first rule we will examine comes from the most common case of function calls: standalone function invocation. Think of this this rule as the default catch-all rule when none of the other rules apply.
```js
function foo() {
	console.log( this.a );
}

var a = 2;

foo(); // 2

```
The first thing to note, if you were not already aware, is that variables declared in the global scope, as var a = 2 is, are synonymous with global-object properties of the same name. They're not copies of each other, they are each other. Think of it as two sides of the same coin.
Secondly, we see that when foo() is called, this.a resolves to our global variable a. Why? Because in this case, the default binding for this applies to the function call, and so points this at the global object.
```js
function foo() {
	"use strict";

	console.log( this.a );
}

var a = 2;

foo(); // TypeError: `this` is `undefined`
```
A subtle but important detail is: even though the overall this binding rules are entirely based on the call-site, the global object is only eligible for the default binding if the contents of foo() are not running in strict mode; the strict mode state of the call-site of foo() is irrelevant.
```js
function foo() {
	console.log( this.a );
}

var a = 2;

(function(){
	"use strict";

	foo(); // 2
})();
```

## 2.Implicit Binding
Another rule to consider is: does the call-site have a context object, also referred to as an owning or containing object, though these alternate terms could be slightly misleading.

Consider:
```js
function foo(){
  console.log(this.a);
 }
 var obj = {
  a:2,
  foo:foo
};
obj.foo(); //2
```
Firstly, notice the manner in which foo() is declared and then later added as a reference property onto obj. Regardless of whether foo() is initially declared on obj, or is added as a reference later (as this snippet shows), in neither case is the function really "owned" or "contained" by the obj object.
However, the call-site uses the obj context to reference the function, so you could say that the obj object "owns" or "contains" the function reference at the time the function is called.

Only the top/last level of an object property reference chain matters to the call-site. For instance:
```js
function foo(){
  console.log(this.a);
}
var obj2 = {
  a:42,
  foo:foo
};

var obj1 = {
  a:2,
  obj2:obj2
}
obj1.obj2.foo(); // 42
```

### Implicitely lost
One of the most common frustrations that this binding creates is when an implicitly bound function loses that binding, which usually means it falls back to the default binding, of either the global object or undefined, depending on strict mode.
```js
function foo(){
  console.log(this.a);
}
var obj ={
  a:1,
  foo:foo
};
var a = "oops, global" // a is also a property in global
setTimeout( obj.foo, 100 ); // "oops, global"

```
## Explicit Binding
With implicit binding as we just saw, we had to mutate the object in question to include a reference on itself to the function, and use this property function reference to indirectly (implicitly) bind this to the object.
But, what if you want to force a function call to use a particular object for the this binding, without putting a property function reference on the object?
JS has call, apply methods to explicitely bind an object to this keyword.
```js
function foo() {
	console.log( this.a );
}

var obj = {
	a: 2
};

foo.call( obj ); // 2
```
Invoking foo with explicit binding by foo.call(..) allows us to force its this to be obj.

Unfortunately, explicit binding alone still doesn't offer any solution to the issue mentioned previously, of a function "losing" its intended this binding, or just having it paved over by a framework, etc.
### Hard Binding
But a variation pattern around explicit binding actually does the trick. Consider:
```js
function foo() {
	console.log( this.a );
}

var obj = {
	a: 2
};

var bar = function() {
	foo.call( obj );
};

bar(); // 2
setTimeout( bar, 100 ); // 2

// `bar` hard binds `foo`'s `this` to `obj`
// so that it cannot be overriden
bar.call( window ); // 2
```
The most typical way to wrap a function with a hard binding creates a pass-thru of any arguments passed and any return value received:
```js
function foo(something) {
	console.log( this.a, something );
	return this.a + something;
}

var obj = {
	a: 2
};

var bar = function() {
	return foo.apply( obj, arguments );
};

var b = bar( 3 ); // 2 3
console.log( b ); // 5
```
Another way to express this pattern is to create a re-usable helper:
```js
function foo(something) {
	console.log( this.a, something );
	return this.a + something;
}

// simple `bind` helper
function bind(fn, obj) {
	return function() {
		return fn.apply( obj, arguments );
	};
}

var obj = {
	a: 2
};

var bar = bind( foo, obj );

var b = bar( 3 ); // 2 3
console.log( b ); // 5
```

Since hard binding is such a common pattern, it's provided with a built-in utility as of ES5: Function.prototype.bind, and it's used like this:

```js
function foo(something) {
	console.log( this.a, something );
	return this.a + something;
}

var obj = {
	a: 2
};

var bar = foo.bind( obj );

var b = bar( 3 ); // 2 3
console.log( b ); // 5
```
bind(..) returns a new function that is hard-coded to call the original function with the this context set as you specified.
Note: As of ES6, the hard-bound function produced by bind(..) has a .name property that derives from the original target function. For example: bar = foo.bind(..) should have a bar.name value of "bound foo", which is the function call name that should show up in a stack trace.
### API context

Many libraries' functions, and indeed many new built-in functions in the JavaScript language and host environment, provide an optional parameter, usually called "context", which is designed as a work-around for you not having to use bind(..) to ensure your callback function uses a particular this.

For instance:
```js
function foo(el) {
	console.log( el, this.id );
}

var obj = {
	id: "awesome"
};

// use `obj` as `this` for `foo(..)` calls
[1, 2, 3].forEach( foo, obj ); // 1 awesome  2 awesome  3 awesome
```
Internally, these various functions almost certainly use explicit binding via call(..) or apply(..), saving you the trouble.

## new Binding
There's really no such thing as "constructor functions", but rather construction calls of functions.
When a function is invoked with new in front of it, otherwise known as a constructor call, the following things are done automatically:

1.a brand new object is created (aka, constructed) out of thin air
2.the newly constructed object is [[Prototype]]-linked
3.the newly constructed object is set as the this binding for that function call
4.unless the function returns its own alternate object, the new-invoked function call will automatically return the newly constructed object.
```js
function foo(a) {
	this.a = a;
}

var bar = new foo( 2 );
console.log( bar.a ); // 2
```
By calling foo(..) with new in front of it, we've constructed a new object and set that new object as the this for the call of foo(..). So new is the final way that a function call's this can be bound. We'll call this new binding.

## Everything in order
So, now we've uncovered the 4 rules for binding this in function calls. All you need to do is find the call-site and inspect it to see which rule applies. But, what if the call-site has multiple eligible rules? There must be an order of precedence to these rules.

**It should be clear that the default binding is the lowest priority rule of the 4. So we'll just set that one aside.**
Which is more precedent, implicit binding or explicit binding? Let's test it:
```js
function foo(){
	console.log(this.a);
}
var obj1 = {
	a:2,
	foo:foo
}

var obj2 = {
	a:3,
	foo:foo
}

obj1.foo(); //2
obj2.foo(); //3

obj1.foo.call(obj2); //3
obj2.foo.call(obj1); //2

```
So, **explicit binding takes precedence over implicit binding**, which means you should ask first if explicit binding applies before checking for implicit binding.

Now, we just need to figure out where new binding fits in the precedence.
```js
function foo(){
	this.a = something;
}
var obj1 = {
	foo:foo;
}
var obj2 = {}

obj1.foo( 2 );
console.log( obj1.a ); // 2

obj1.foo.call( obj2, 3 );
console.log( obj2.a ); // 3

var bar = new obj1.foo( 4 );
console.log( obj1.a ); // 2
console.log( bar.a ); // 4
```
OK, new binding is more precedent than implicit binding. But do you think new binding is more or less precedent than explicit binding?

**Note:** new and call/apply cannot be used together, so new foo.call(obj1) is not allowed, to test new binding directly against explicit binding. But we can still use a hard binding to test the precedence of the two rules.

By that reasoning, it would seem obvious to assume that hard binding (which is a form of explicit binding) is more precedent than new binding, and thus cannot be overridden with new.

```js
]function foo(something) {
	this.a = something;
}

var obj1 = {};

var bar = foo.bind( obj1 );
bar( 2 );
console.log( obj1.a ); // 2

var baz = new bar( 3 );
console.log( obj1.a ); // 2
console.log( baz.a ); // 3
```
Whoa! bar is hard-bound against obj1, but new bar(3) did not change obj1.a to be 3 as we would have expected. Instead, the hard bound (to obj1) call to bar(..) is able to be overridden with new. Since new was applied, we got the newly created object back, which we named baz, and we see in fact that baz.a has the value 3.

The primary reason for this behavior is to create a function (that can be used with new for constructing objects) that essentially ignores the this hard binding but which presets some or all of the function's arguments. One of the capabilities of bind(..) is that any arguments passed after the first this binding argument are defaulted as standard arguments to the underlying function (technically called "partial application", which is a subset of "currying").

```js
function foo(p1,p2) {
	this.val = p1 + p2;
}

// using `null` here because we don't care about
// the `this` hard-binding in this scenario, and
// it will be overridden by the `new` call anyway!
var bar = foo.bind( null, "p1" );

var baz = new bar( "p2" );

baz.val; // p1p2
```
## Determining this
Now, we can summarize the rules for determining this from a function call's call-site, in their order of precedence. Ask these questions in this order, and stop when the first rule applies.

1. Is the function called with new (new binding)? If so, this is the newly constructed object.

`var bar = new foo()`

2. Is the function called with call or apply (explicit binding), even hidden inside a bind hard binding? If so, this is the explicitly specified object.
`var bar = foo.call( obj2 )`
3. Is the function called with a context (implicit binding), otherwise known as an owning or containing object? If so, this is that context object.

`var bar = obj1.foo()`
4. Is the function called with a context (implicit binding), otherwise known as an owning or containing object? If so, this is that context object.

`var bar = obj1.foo()`

