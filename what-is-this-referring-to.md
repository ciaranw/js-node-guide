What is this referring to?
--------------------------

A major source of confusion in Javascript is around the value of `this` inside functions. The truth is that due to the dynamic nature of the language, the value of `this` is not constant. There are, however, some rules that are fairly simple.

The general rule of thumb is that the value of `this` in a function is equal to the context from which the function was invoked. That phrase is a bit wordy, so let me try to explain with an example:

```js
function standalone() {
    console.log(this);
}

standalone(); // 'this' refers to the 'global' object. in browsers, that is the 'window' object
              // in node.js, it is an object called 'global'

// here, we're assigning the 'standalone' function to a property on 'obj'
var obj = {
    standalone: standalone
};

obj.standalone(); // 'this' now refers to 'obj', since 'obj' was the context
                  // on which the function was called
```

You can also override the value of `this` by binding a context to a function. To do this, we use the `bind` method. This will return a new function that always has a fixed value for `this`.

```js
var func = function() {
    console.log(this);
};

var obj1 = {
    name: 'obj1'
};

var bound = func.bind(obj1);

bound(); // outputs '{name: 'obj1'}'

var obj2 = {
    name: 'obj2'
};

obj2.func = bound;

// this still prints out '{name: 'obj1'}' even though the calling context is obj2
obj2.func();
```

There are a few things to note about `bind`. The first point is that `bind` is not available to use in all browser environments. You can safely use it in IE9+ (as well as all decent modern browsers), and it can always be used in node.js. If you wish to use `bind`-like functionality in the browser, it is best to use a framework-provided shim (`$.proxy` from jQuery, `_.bind` from underscore). The second point is that `bind` has a small performance penalty, so it is best avoided in tight loops.

#### Notes about jQuery event handlers and `this`

One of the big confusions I've seen around `this` stems from its usage in jQuery event handlers. As a rule of thumb, when you attach an event listener to a DOM element (for example, a click handler) the value of `this` will be the DOM element on which the event was triggered. This is often fairly useful as you have a reference to the element you are handling, which means you don't have to store a reference elsewhere. However, if your event handler function belongs to an object, you sometimes need to get a reference to that object inside the handler. You can solve this problem in a number of ways:

You can use `bind` (or the `$.proxy` function, since you're already using jQuery):

```js
var obj = {
    name: 'obj1',
    onClick: function(e) {
        console.log('click, name is ' + this.name);
    }
};

$('#button').click($.proxy(obj.onClick, obj));
```

You can also store a reference to the object and use a closure to capture the object in scope:

```js
var obj = {
    name: 'obj1',
    onClick: function(e) {
        console.log('click, name is ' + this.name);
    },
    init: function() {
        var self = this;
        $('#button').click(function(e) {
            self.onClick(e);
        });
    }
};
```

Be careful with closures! They can very easily create memory leaks since they close over the scope of variables.