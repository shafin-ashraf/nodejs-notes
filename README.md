## Promise
A promise is an object that embodies the eventual result or error of a asynchronous operation
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
## Tips
- Nodejs ``util`` module has a ``promosify`` function that turns any callback based function into promise based