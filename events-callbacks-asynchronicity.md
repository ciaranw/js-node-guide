Events, callbacks and asynchronicity
------------------------------------

Unlike many languages that handle multiple threads of execution, Javascript is inherintly single-threaded. So how do we handle situations where we can handle multiple things happening at once? The truth is that we actually don't handle multiple things happening at once. All Javascript execution is sequential. We use a technique called _Event Driven Programming_ to give the illusion of concurrency.

#### Event loops

For those who have ever done any game programming will be familiar with the concept of event loops. For those who don't, here is an overview, in psudocode:

```
while (true) {
    events = get_events_fired_last_tick()
    for each event in events {
        handle_event(event)
    }

    tick_event_loop()
}
```

We grab all the events that fired on the last tick of the loop, and handle them. The `handle_event()` function will pass the event to anything that has registered a listener for that event. Those listeners can fire additional events that will be handled on the next round of the loop. Finally, we signal that this round of the loop has ended, or in other words, that the loop has ticked. This process is repeated forever, or until the process is terminated.

Javascript, in browser scripts and on the server via node.js, uses an event loop. The typical events you see in the browser are ones triggered by DOM elements like clicks, mouse movement, form submissions etc, whilst in node.js they are typically events triggered by I/O like http requests or data read from a file on disk.

#### All the javascripts in ur servers LOL!!1!!1!

So, if a Javascript process is single threaded and sequential, how can we create a useful server that can serve requests to more than a single person at a time? In most modern server applications, the amount of time spent waiting for I/O dwarfs the time spent on the CPU calculating things. If I/O is _asynchronous_ and _non blocking_ then we can use the time normally spent blocking execution on I/O, on more CPU-bound work! This will allow us to quickly execute the small amount of CPU work needed to process a request and move on to the next. Ok, enough with the theory, lets see some code:

```js
var http = require('http');

// lets create a server. The function you pass into 'createServer' will
// be bound to the 'request' event fired by the server object. This event
// gets fired once a http request is made to the server.
var server = http.createServer(function(req, res) {
    // here is our CPU-bound computation. for now, we'll just return
    // some text to the client
    res.end('Hello world!');
});

// the server doesn't start accepting connections until you ask it to
// listen to a port
server.listen(8888, function() {
    // this function is called when the server has successfully bound to a port
    console.log('server started'); 
});
```

What you see above is a very simple server that responds with 'Hello world!' when you send any http request to it. There are 2 examples of evented programming here. The first is the function we pass to the `http.createServer` function. This function will get called whenever a HTTP request is sent to the server. Here, we're setting up a _callback_ - we only run that function at the time that the `request` event is fired. The second is the function that we pass to the `server.listen` function. This function gets called when the server has bound to a port and is accepting connections.

One thing to note here is that the two callback functions are guaranteed to be called after the script has run to completion - i.e. from top to bottom. This allows you to set up any event listeners or callbacks before the server starts accepting connections. This way, there are no tricky race conditions to worry about. In fact, because Javascript is always single-threaded, it is very easy to reason about concurrency in your scripts. You do not have the threat of the thread scheduler suspending your program and letting another thread stomp over all your state.

One word of warning - because Javascript will always try and run a script to completion, if you spend too long processing something you will block the event loop and it will not be able to process any new events. If your program requires lengthy computational operations then node.js is probably not a good choice for your problem.