Classes via prototypes
----------------------

One of the things I see a lot when developers that are familiar with Object-Oriented programming come to Javascript is that they don't get prototypes. This is especially apparent with Java developers, since there are a lot of simularities in the syntax of the two languages. They complain that they don't understand how to do classes and that they have to use global state and loads of global functions. Let me show you how to do simple classes in Javascript. Here is a quick example:

```js
var MyClass = function(name) {
    this.name = name;
};

MyClass.prototype.method = function() {
    console.log('this is a method on MyClass. My name is ' + this.name);
};

var instance = new MyClass('bob');

// prints out 'this is a method on MyClass. My name is bob'
instance.method();
```

And that's all there is to it.

#### Constructors

In Javascript, we use functions as constructors. There's nothing special about the function, it doesn't need to have a special name or follow any convention, although most programmers will give it a `PascalCase` name. When you call the function with the `new` keyword, it will create and return a new instance. Inside the function, `this` refers to the new instance being created.

#### Methods

To create 'instance' methods, attach them to the `prototype` of the constructor function you just made.  All the methods attached to the prototype will be available in the constructor function. The value of 'this' will almost always be the instance itself. We'll touch on how `this` works in another post.

Note that variables and methods on a class can overwrite each other. If you declare a `name` method on a prototype and then add a `name` variable, the latest declared one wins. There are no public and private scopes in Javascript. Here is an example:

```js
var Funky = function() {
    this.name = 'Jaco'; // this will overwrite the method defined below
                        // when the class is instantiated
};

Funky.prototype.name = function() {
    return this.name; // this will return the 'name' function, i.e. itself.
};
```

#### Extending classes

Generally, if you're trying to solve a problem by creating a class hierachy, you're going about it the wrong way. Have you thought about using composition instead? However, since Javascript is a dynamic language, we can compose objects easily using a technique called 'mixins'. The way this works is that we take all the methods defined on the prototype of the thing you want to mix in and add them to the 'class' that we're creating. This will make those methods available as if they were declared on the class itself. We also make sure to call the constructor function of the mixin inside our constructor function, so we get all of the mixin constructor's functionality and instance variables.

If you're developing for node.js, you can use the handy 'util' module to do this:

```js
var util = require('util');

var MyMixin = function(colour) {
    this.colour = colour;
};

MyMixin.prototype.sayColour = function(text) {
    console.log(text + ', with colour ' + this.colour);
};

var MyClass = function(name, colour) {
    this.name = name;
    MyMixin.call(this, colour);
};

util.inherits(MyClass, MyMixin);

var composed = new MyClass('ted', 'blue');

// this prints 'hi, with colour blue'
composed.sayColour('hi');
```