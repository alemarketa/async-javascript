# Quizz 1

Using you're knowledge of the event loop, create a program which prints out the below. If the log line mentions a `setInterval` it must be printed inside a `setInterval` etc..

start
end
setInterval 1
promise 1
promise 2

```js
console.log("start");
let interval = setInterval(() => {
  console.log("setInterval 1");
  Promise.resolve().then(()=> console.log("promise 1")).then(()=> {
    console.log("promise 2");
    clearInterval(interval);})
}, 0);

console.log("end");
```

# Quizz 2

Extend the previous example to print out the following log lines, use `process.nextTick` and `setImmediate`

start
end
setInterval 1
promise 1
promise 2
processNextTick 1
setImmediate 1
promise 3
promise 4
