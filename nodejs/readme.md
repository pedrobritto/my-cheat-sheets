# Node.js cheatsheet

This is just my Node.js cheatshseet.

### What is node

Node is a **runtime environment** for JavaScript, build around Chrome's V8 engine (a engine used to read and convert JS code to machine code inside Google Chrome). It doesn't run inside a browser though, but a C++ wrapper, which means it cannot use browser specific features (as the `window` object or `querySelector`s. But it has some node specific new objects and methods, as the `http` module, that can be used to create and route http servers and the `fs` (file system) module, that can read and write files in the system.

It is single threaded and non-blocking I/O. Being single threaded means it's not very good with CPU-intensive tasks, but being non-blocking means it can handle lots of simultaneous requests. For example, while one requests was initially handled and is now waiting a response from an external server, it can go and work on a second resquest, queueing requests as needed, without wasting time.

### When you should and shouldn't node

Node is great for disk and network I/O (input/output) intensive tasks (like handling webserver requests) and realtime applications (like live chats).

It's not a good choice, however, for CPU-intensive tasks, like video encoding or image processing due to being single threaded (and boy, theses tasks will punish your single thread), thus not being able to make good use of the non-blocking I/O.

## Important modules

### Path (`path`)

> The path module provides utilities for working with file and directory paths.

### OS - Operating System (`os`)

> The os module provides a number of operating system-related utility methods.

### File Sytem (`fs`)

> File I/O is provided by simple wrappers around standard POSIX functions. All the methods have asynchronous and synchronous forms.

## Events

Events are a core component of Node.js.

> Much of the Node.js core API is built around an idiomatic asynchronous event-driven architecture in which certain kinds of objects (called "emitters") periodically emit named events that cause Function objects ("listeners") to be called.

Example of event usage:

```js
const EventEmitter = require("events");
const event = new EventEmitter();

// Register an event listener to the eventName event.
event.on("eventName", eventArg => {
  // Do this when eventName is emitted.
});

/**
 * Emits/triggers the eventName event.
 * @param type {string|number}
 * @param ...args {any} eventArg
 */
event.emit("eventName", { id: 1, name: "eventArg" });
```

As we work in a modular way, the listener and emitter would most likely end up in different files. So the best approach is to create a custom class that extends the `EventEmitter` instead of creating a new instance of it in every file.

A real use-case would be the following:

```js
// logger.js

const EventEmitter = require("events");

// Custom class extending EventEmitter
class Logger extends EventEmitter {
  log(message) {
    console.log(message);

    this.emit("messageLogged", { data: "data content" });
  }
}

module.exports = Logger;
```

```js
// app.js

const Logger = require("./logger");
const logger = new Logger();

// This has to come before the logger.emit() execution
logger.on("messageLogged", () => {
  console.log("Do this when messageLogged triggers!");
});

// Executing the Logger's class instance
logger.log("Logging...");
```

## Basics

### A simple, pure node webserver

```js
const http = require("http"); // Requires the http module
const fs = require("fs"); // Requires the fs (file system) module

// Some server-related variables
const hostname = "127.0.0.1";
const port = 3000;

// Serving the index.html file.
fs.readFile("./index.html", (err, data) => {
  if (err) throw err;

  // If no error is thrown, a webserver is created using http's createServer() method.
  // Here we can pass a status code, request header and the data to be served.
  const server = http.createServer((req, res) => {
    res.statusCode = 200;
    res.setHeader("Content-Type", "text/html");
    res.end(data);
  });

  // After req and res parameters are set, the server can be executed with the listen() method.
  server.listen(port, hostname, () =>
    console.log(`Server running at http://${hostname}:${port}/`)
  );
});
```

## Other stuff

### `global`, not `window`

There's no `window` object in the node runtime. Instead, it has a `global` object that works similarly.

### `module` object

Contains information about the current module, such as id, exports and so on.

### Module wrapper function

Node doesn't directly executes code inside moduels. Instead, it wraps every module inside a **module wrapper function**.

It looks like this:

```js
(function(exports, require, module, __filename, __dirname) {
  // your code ...
});
```

It's basicaly an IFFE with a bunch of arguments that executes your code and you can acctually see those arguments contents by logging them.

The `__filename` and `__dirname` arguments are the full path to the file, including and exclusing the file name, respectively.
