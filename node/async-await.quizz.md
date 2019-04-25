# Question 1

Convert the promise version of the multi-file loader over to using async/await

```js
const util = require("util");
const fs = require("fs");
const readFile = util.promisify(fs.readFile);

let filesss= ["node/files/demofile.txt", "node/files/demofile.other.txt"];


// read both files simultaneously

  // let promises = files.map(name => readFile(name, { encoding: "utf8" }));
  // Promise.all(promises).then(values => {
  //   // <-- Uses .all
  //   console.log(values);
  // });

/* answer:
but read one file and then read the other. Bad performance */

(async () => {
  for (let file of filesss) {
    let value = await readFile(file,"utf8")
    console.log(value)
  }
})()

/* better answer, combining await/async and Promises */

(async () => {
    let promises = filesss.map(name => readFile(name, "utf8"))
    let values = await Promise.all(promises)
    console.log(values)
})()

```

# Question 2

Again convert the promise version of the multi-file loader over to using async/await but using a custom async iterator with the following syntax

node --harmony-async-iteration <file.js>

```js
const util = require('util');
const fs = require('fs');
const readFile = util.promisify(fs.readFile);


const fileIterator = files => ({
  [Symbol.asyncIterator]: () => ({
    x: 0,
    next() {
      // end condition
      if (this.x >= files.length) {
        return Promise.resolve({done: true});
      }

      let file = files[this.x++];
      return readFile(file, 'utf8').then(data => ({
        done: false,
        value: data
      }))
      //{ return { done: false, value: data}})
    }
  })
});

(async () => {
  for await (let x of fileIterator([
    "node/files/demofile.txt",
    "node/files/demofile.other.txt"
  ])) {
    console.log(x);
  }
})();
```
