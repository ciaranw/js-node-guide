Don't use these bits of Javascript
----------------------------------

Javascript is a simple, lightweight and expressive language. With an ugly and unpredictable language bolted on the side. The key to understanding and enjoying the language is knowing which bits to use and forgetting everything else exists.

#### Don't let Javascript coerce your types

Equality (i.e. `==`) is totally messed up in Javascript. You should, apart from one exception, always use they type-checking equality operator, `===`. This will do exactly what you expect it to do, rather than the weird rules and edgecases that `==` has. For example:

```js
1 === 1;           // true
1 === '1';         // false
'false' === false; // false
'123' !== 123      // true
// you get the idea...
```

The one exception to this rule is when checking for `null` or `undefined`. In Javascript, we can generally use either one, but they are not equal.This is because `null` and `undefined` are of different types.

```js
null === undefined; // false
typeof(null);       // 'object'
typeof(undefined);  // 'undefined'
```

We normally want to treat these two as equal, so in this case, we would use the type-coercing equality operator:

```js
var test;     // this is undefined
test == null; // true

test = 'hi there';
test != undefined; //true
```

#### Always remember to use `var`

When declaring variables, make sure that you always use the `var` keyword. This is because variables that are not declared in this way are automatically made global. Global variables are a really bad thing, as you are all aware.

```js
myVar = 'test'; // this is now a global variable. instead, do this:
var myVar = 'thats better';

// once a variable is declared, you can refer to it
// Some people like to declare their variables up front; this is ok
var test1, test2;

test1 = 'something';
test2 = 9876;
```

#### There is no block scoping, only function scoping

Programmers coming from languages like Java get caught out on this one. In Java, you can declare a variable in an `if` statement and it will be scoped to that statement. That is, you cannot reference it outside of the `if` statement. Javascript does not behave like this.

```js
if (test) {
    var myVar = 'inside the if statement';
}

// if this was Java, this would be a compiler error
// What actually happens is that we print out
// 'inside the if statement' to the console
console.log(myvar);
```

In Javascript, the only way to scope a variable is to declare it inside a function. This is exactly why sometimes you see the main body of a javascript program enclosed inside a function:

```js
// here we declare what is known as an Immediately-Invoked Function Expression (or IIFE for short)
// we enclose the function in parenthesis so that we can invoke it straight away 
(function() {
    var myVar = 'test';
})(); // here the function is invoked

console.log(myVar); // this prints out undefined

// you can also pass arguments into the IIFE:
(function($) {
    $(document).ready(function() {
        // my document has loaded
    });
})(jQuery);
// we pass in the jQuery object, and it is available in the function as the
// first argument in the function above
```
