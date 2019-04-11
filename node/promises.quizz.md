# Question 1 - (10min)

Create a promise version of the async readFile function

```js
const fs = require("fs");
const util = require("util")
const readFile = util.promisify(fs.readFile)

/**
* commented the promise because of using util.promisify
function readFile(filename, encoding) {
  return new Promise((resolve, reject) => {
    fs.readFile(filename, encoding, (err, data) => {
      if(err) {
        reject(err)
      }
      else {
      resolve(data)
      }
    }); 
  });
}
 */

readFile("node/files/demofile.txt", "utf-8").then(
  data => console.log("File Read", data),
  err => console.log("Failed to Read the File", err)
  )

/* previous callback
function readFile(filename, encoding) {
  fs.readFile(filename, encoding, (err, data) => {
    //TODO
  });
}
readFile("./files/demofile.txt", "utf-8")
    .then(...) -->
*/
```

# Question 2

Load a file from disk using readFile and then compress it using the async zlib node library, use a promise chain to process this work.

```js
const fs = require("fs");
const zlib = require("zlib");

function gzip(data) {
  return new Promise((resolve, reject) => {
    zlib.gzip(data, (err, result) => {
      if(err) return reject(err);
      resolve(result)
    });
  });
}

function readFile(filename, encoding) {
  return new Promise((resolve, reject) => {
    fs.readFile(filename, encoding, (err, data) => {
      if (err) reject(err);
      resolve(data);
    });
  });
}

readFile("node/files/demofile.txt", "utf-8").then( 
    data => {
      return gzip(data);
    }, 
    err => err => console.error("Failed to Read", err)
  ).then( 
    data => {
      console.log(data);
    },
    err => console.error("Failed: ", err)
  );
```

# Question 3

Convert the previous code so that it now chains the promise as well.

```js
const fs = require("fs");
const zlib = require("zlib");

function gzip(data) {
  return new Promise((resolve, reject) => {
    zlib.gzip(data, (err, result) => {
      if(err) return reject(err);
      resolve(result)
    });
  });
}

function readFile(filename, encoding) {
  return new Promise((resolve, reject) => {
    fs.readFile(filename, encoding, (err, data) => {
      if (err) reject(err);
      resolve(data);
    });
  });
}

readFile("node/files/demofile.txt", "utf-8").then( 
  data => {
    // needs to return otherviwe the initial data are repeated in the next .then
    return gzip(data);  
  },
  err => console.error('Failed to Read', err)).then(
      res => {
        console.log(res);
      }, 
      err => console.error("Failed ro Gzip", err)
    );
    
```

# Question 4

Convert the previous code so that it now handles errors using the catch handler

```js
const fs = require("fs");
const zlib = require("zlib");

function gzip(data) {
  return new Promise((resolve, reject) => {
    zlib.gzip(data, (err, result) => {
      if(err) return reject(err);
      resolve(result)
    });
  });
}

function readFile(filename, encoding) {
  return new Promise((resolve, reject) => {
    fs.readFile(filename, encoding, (err, data) => {
      if (err) reject(err);
      resolve(data);
    });
  });
}

readFile("node/files/demofilee.txt", "utf-8").then(data => {
    return gzip(data);  
  }).then(res => {
  console.log(res);
  }).catch(err => console.error('Some error', err));
    
```



# Question 5

Create some code that tries to read from disk a file and times out if it takes longer than 1 seconds, use `Promise.race`

```js
function readFileFake(sleep) {
  return new Promise(resolve => setTimeout(resolve, sleep, "read"))
}

function fakeTimeOut(sleep) {
  return new Promise((_, reject) => setTimeout(reject, sleep, "timeout"))
}
 // This resolves a promise after 5 seconds, pretend it's a large file being read from disk

Promise.race([readFileFake(5000), fakeTimeOut(1000)]).then(
  value => { console.log("Resolved", value)}).catch( 
  err => {console.log("Rejected", err)})


```


# Question 6

Create a process flow which publishes a file from a server, then realises the user needs to login, then makes a login request, the whole chain should error out if it takes longer than 1 seconds. Use `catch` to handle errors and timeouts.

```js
function authenticate() {
  console.log("Authenticating");
  return new Promise(resolve => setTimeout(resolve, 2000, { status: 200 }));
}

function publish() {
  console.log("Publishing");
  return new Promise(resolve => setTimeout(resolve, 2000, { status: 403 }));
}

function timeout(sleep) {
  return new Promise((resolve, reject) => setTimeout(reject, sleep, "timeout"));
}

function correctPublish() {
  return publish().then( res => {
    if(res.status === 403) {
      return authenticate();
    }
    return res
  })
}

//  fail correctly even with authentication
Promise.race([correctPublish(), timeout(3000)]).then(res => console.log('Published')).catch(err => {
  if (err === "timeout") {
    console.log('Time out')
  }
  else {
    console.log('Error', err)
  }
});


// takes in account only publish, authenticate can take here x time and it won't time out
Promise.race([publish(), timeout(1000)]).then( res => { 
  if( res.status === 403) { 
    return authenticate()
  }
}).then(res => console.log('Published')).catch(err => {
  if (err === "timeout") {
    console.log('Time out')
  }
  else {
    console.log('Error', err)
  }
});



```
