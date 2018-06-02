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

**It's a common mis-statement that "everything in JavaScript is an object". This is clearly not true. **
