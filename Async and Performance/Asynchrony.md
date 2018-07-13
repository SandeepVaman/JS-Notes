# Asynchrony
The relationship between the now and later parts of your program is at the heart of asynchronous programming.

The simplest (but definitely not only, or necessarily even best!) way of "waiting" from now until later is to use a function, commonly called a callback function:
```

// ajax(..) is some arbitrary Ajax function given by a library
ajax( "http://some.url.1", function myCallbackFunction(data){

	console.log( data ); // Yay, I gots me some `data`!

} );
```
**Warning**: You may have heard that it's possible to make synchronous Ajax requests. While that's technically true, you should never, ever do it, under any circumstances, because it locks the browser UI (buttons, menus, scrolling, etc.) and prevents any user interaction whatsoever. This is a terrible idea, and should always be avoided.

Before you protest in disagreement, no, your desire to avoid the mess of callbacks is not justification for blocking, synchronous Ajax.

For example, consider this code:




