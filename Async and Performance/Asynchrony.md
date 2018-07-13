# Asynchrony
The relationship between the now and later parts of your program is at the heart of asynchronous programming.

The simplest (but definitely not only, or necessarily even best!) way of "waiting" from now until later is to use a function, commonly called a callback function:
```

// ajax(..) is some arbitrary Ajax function given by a library
ajax( "http://some.url.1", function myCallbackFunction(data){

	console.log( data ); // Yay, I gots me some `data`!

} );
```
