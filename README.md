# SRPC

Simplest Server-Client Communication for both JavaScript and Python! The best code for communication is *no* code for communication.

**NO dependency, NO schema, NO config, just define functions and CALL!**

**Support both servers and clients of JavaScript and/or Python!**

```python
# On Python Server
from server import srpc
srpc() # start server on port 11111
srpc["add"] = lambda x, y: x + y
```

```js
// On Browser Client
import srpc from './client-es.js'
srpc('http://localhost:11111/') // server endpoint
console.log(await srpc.add(1, 2)) // 3
```

## Server

Export functions to be called by clients

### Nodejs Server

> Copy the file `server-es.js` or `server-common.js` to your project

```js
import srpc from './server-es.js'
// or using CommonJS
// const srpc = require('./server-common.js')

srpc() // listen on port 11111 by default

// following methods are exported
srpc.test = () => 'Hello, world!'
srpc.add = (x, y) => x + y
srpc.calc = {} // function can be nested!
srpc.calc.sqrt = x => Math.sqrt(x)
```

### Python Server

> Copy the file `server.py` to your project

```python
from server import srpc

srpc() # listen on port 11111 by default

# Python dict uses []
srpc["test"] = lambda: "Hello, world!"
def add(x, y):
    return x + y
srpc["add"] = add
import math
srpc["calc"] = { "sqrt": math.sqrt }
```

### Aliyun Function Compute Server

> Copy the file `server-fc.js` to your project (only JavaScript FC).

```js
const srpc = require('./server-fc.js')

// following methods are exported
srpc.test = () => 'Hello, world!'
srpc.add = (x, y) => x + y
srpc.calc = {}
srpc.calc.sqrt = x => Math.sqrt(x)

exports.handler = srpc() // entrance
```

## Client

Call functions on server and get the return value

### Browser Client

> Copy the file `client-es.js` to your project, or using CDN.

```js
import srpc from './client-es.js'
// or using CDN in HTML:
// <script src="https://cdn.jsdelivr.net/gh/yzITI/srpc@main/client.js"></script>

srpc('http://localhost:11111/') // initialize with endpoint

// just call the functions!
srpc.test() // Promise -> 'Hello, world!'
srpc.add(1, 2) // Promise -> 3
srpc.calc.sqrt(2) // Promise -> 1.4142135623730951
```

### Nodejs Client

> Copy the file `client-es.js` or `client-common.js` to your project

```js
import srpc from './client-es.js'
// or using CommonJS
// const srpc = require('./client-common.js')

srpc('http://localhost:11111/') // initialize with endpoint

srpc.test() // Promise -> 'Hello, world!'
srpc.add(1, 2) // Promise -> 3
srpc.calc.sqrt(2) // Promise -> 1.4142135623730951
```

### Python Client

> Copy the file `client.py` to your project

```python
from client import srpc

srpc('http://localhost:11111/')

# Python takes dictionary syntax
srpc["test"]() # 'Hello, world!'
srpc["add"](1, 2) # 3
# dot also works for Python client
srpc.calc.sqrt(2) # 1.4142135623730951
```

## Advanced

For development and customization.

### Protocol Model

The following request and response model are used with http `POST` method and `'Content-Type': 'application/json'`. **All function names and arguments must be JSON serializable.**

```js
Request {
  N: ['nested', 'f'], // function name
  A: [1, 2, 3] // arguments in order
}
Response {
  R: {} // function return
}
// Context used in hooks
Context {
  N, A, R,
  IP: String, // request IP
  F: Function
}
```

### Server Options

```js
// Nodejs Server
srpc(hooks = {
  before: Context => {}, // abort if assign Context.R
  after: Context => {}
}, port = 11111)

// Aliyun Function Compute Server
srpc(hooks = {
  before: Context => {}, // abort if assign Context.R
  after: Context => {}
})
```

```python
srpc(hooks={
  "before": Function(Context),
  "after": Function(Context)
}, port=11111)
```
