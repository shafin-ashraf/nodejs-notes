## Callback
A callback is a function that is passed as an argument to another function and is invoked
with the result with the opearation completes. It is also knows as ``continuation-passing-style`` or ``CPS``
### Example
```Javascript
// direct call style
function sum(a, b) {
    return a + b
}
console.log(`Summation with direct call: ${sum(1, 2)}`)

// continuation passing style
function sumWithCPS(a, b, callback) {
    return callback(a + b)
}

function callback1(sum) {
    console.log(`Summation with CPS: ${sum}`)
}
sumWithCPS(1, 2, callback1)
```
### Why callbacks?
In most other language concurrency is done by using multiple threads. But during io; cpu cycle is wasted because current thread is waiting for io. In node concurrency is handled differently. Node executes all non io related function in a single thread. When this thread is waiting for io nodejs eventloop dispatches another function from call stack so that cpu cycle is utilized properly. This asynchronous type of code cannot be written in synchronous way (assuming we still don't know what is async/await), thats why callback function is used, so that eventlopp can dispatch this function in future.
### Problems with callback (callback hell)
Most of the time we want to execute some code after some code. In synchrous code this is very easy, but in callback you have keep nesting functions to perform one block of code after another tbis is known as callback hell
```javascript
getUser(1, (user) => {
  console.log("User", user);
  getRepositories(user.githubUsername, (repos) => {
    console.log(repos);
    getCommits(repos[0], (commits) => {
      console.log(commits);
      // Callback Hell ("-_-)
    }
})
```
Code above is short example of callback hell. This can get much worse for example
![callback hell](callback_hell.jpeg)
### Mitigating callback
- Instead of defining anonymous function as callback, give function a name and move it outside it will keep code shallow
```javascript
    callbackFromHell(param, function (arg1, arg2) {
        // code
        // code
    })

    // giving callback function a name and moving it outside
    callBackFromHell(param, callback)

    function callback() {
        // code
        // code
    }
```
- Learn and use Promise
- Use async function with await keywords
## Promise
A promise is an object that embodies the eventual result or error of a asynchronous operation
### Example
This example promise has 50-50 chance of resolving or rejecting
```Javascript
const promise = new Promise((resolve, reject) => {
    const randomNumber = Math.random()
    if (randomNumber < 0.5) {
        return resolve('Promise was resolved')
    } else {
        return reject(new Error('Promise was rejected'))
    }
})

promise.then(onResolved, onRejected)

function onResolved(arg) {
    console.log('success')
    console.log(arg)
}

function onRejected(arg) {
    console.log('failed')
    console.log(arg)
}
```
### Promise API
#### Constructor
```javascript
let promise = new Promise((resolve, reject) => {})
```
``resolve(obj)`` will receive result value if promise is fulfilled successfully and ``reject(err)`` will receive instance of an ``Error`` as argument if promise is rejected
### Promise states
- Pending: Asynchronous operation is not yet complete
- Settled: Either fulfilled or rejected
    - Fulfilled: Operation is complete
    - Rejected: Operation terminates with errro

### Instance methods
- ``promise.then(onFulfilled, onRejected)`` Define callbacks for what to do after a promise settles (erither resolved or rejected)
- ``promise.catch(onFulfilled, onRejected)`` Syntactic sugar for promise.then(undefined, onRejected). Useful in promise chaining when want to handle rejected promise at the end
- ``promise.finally(onFinally)`` Runs after a promise is settled (resolved or rejected). Useful to remove duplicate code that needs to be run in both cases resolved or rejected
### Chaining Promise
![Promise chaining](Promise_diagram.png)
### Static methods
- ``Promise.resolve(obj)`` [Description](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise/resolve)
- ``Promise.reject(err)`` Creates a promise that rejects with ``err``
- ``Promise.all(iterable)`` Takes an iterable of promises as an argument and returns a single Promise that resolves to an array of the resolved results of the input promises (Waits for all promise to be resloved or Rejects immediate if a signle promise is rejected).
Note: All promises are run parallelly
- ``Promise.allSettled(iterable)`` Almost like ``Promise.all`` except doesn't reject if any single promise is rejected. Return all settled promises result either resolved or rejected
- ``Promise.race(iterables)`` Returnes the first settled promise from iterables
### Tips
- Nodejs ``util`` module has a ``promosify`` function that turns any callback based function into promise based

## Async/Await
An async function is a special type of function in which it is possible to
use the ``await`` expression to pause the execution on a given promise until it resolves.
At each ``await`` expression the execution of the function is put on hold, its state
is saved and the control is returned to the event loop. Once the ``Promise`` that has
been awaited resolves, the control is given back to the async function, returning the
fulfillment value of the promise. Async function always returns a promise.

## Event Loop
Event loop is an endless loop, where the javascript engine waits for task, executes them and then sleeps, waiting for more tasks. 
The event loop is what allows Node.js to perform non-blocking I/O operations — despite the fact that JavaScript is single-threaded — by offloading operations to the system kernel whenever possible.
### Event Loop example diagram
![Event Loop](event_loop.png)
1. The application generates a new I/O operation by submiing a
request to the Event Demultiplexer. The application also
specifies a handler, which will be invoked when the operation
completes. Submiing a new request to the Event
Demultiplexer is a non-blocking call and it immediately returns
control to the application.
2. When a set of I/O operations completes, the Event
Demultiplexer pushes a set of corresponding events into
the Event Queue.
3. At this point, the Event Loop iterates over the items of the Event
Queue.
4. For each event, the associated handler is invoked.
5. The handler, which is part of the application code, gives back
control to the Event Loop when its execution completes (5a).
While the handler executes, it can request new asynchronous
operations (5b), causing new items to be added to the Event
Demultiplexer (1).
6. When all the items in the Event Queue are processed, the Event
Loop blocks again on the Event Demultiplexer, which then
triggers another cycle when a new event is available.