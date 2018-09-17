# My Express.js Cheat Sheet

## Async Error Handling

### Resources

- https://thecodebarbarian.com/80-20-guide-to-express-error-handling
- http://expressjs.com/en/guide/error-handling.html

All endpoints have to pass along any occourring errors to a error handling middleware by using a async wrapper.

### Async wrapper middleware

```js
// Async wrapper
module.exports = fn => {
  return (req, res, next) => {
    fn(req, res, next).catch(next);
  };
};
```

### Async wrapper middleware usage

```js
const aw = require("../middleware/asyncWrapper");

router.get(
  "/",
  aw(async (req, res) => {
    const document = await Model.find();
    return res.json(document);
  })
);
```

By using the async wrapper function it's not necessary to use `try...catch` blocks to catch exceptions. They will be automaticaly caught by the error handling middleware.

### Error handling middleware

```js
module.exports = (err, req, res, next) => {
  if (!err.message) {
    err.message = "Something went wrong.";
  }

  if (!err.statusCode) {
    err.statusCode = 500;
  }

  return res.status(err.statusCode).json({ error: err.message });
};
```

### Combined code

```js
const app = require("express");
const exampleRoutes = require("./routes/example");
const error = require("./middleware/error");

// In app.js or routes.js
app.use("/example", exampleRoutes);
app.use(error);
```

This way, all errors thrown in the example route will be passed on to the error middleware.

Note: the error middleware should be declared after all routing middleware.

## Alternative Async Error Handling

Async error handling can also be done by using the `express-async-errors` npm package.

No functions are necessary, only the package import on the routes middleware usage.

```js
require("express-async-errors");

// ...

app.use("/example", exampleRoutes);
app.use(error);
```

## Logging

```js
const winston = require("winston");

module.exports = function() {
  winston.configure({
    transports: [
      new winston.transports.Console({ colorize: true, prettyPrint: true }),
      new winston.transports.File({
        filename: "logfile.log"
      })
    ]
  });

  // Uncaught exceptions
  winston.handleExceptions(
    new winston.transports.Console({ colorize: true, prettyPrint: true }),
    new winston.transports.File({ filename: "uncaughtExceptions.log" })
  );

  // Promisse rejection exceptions
  process.on("unhandledRejection", ex => {
    throw ex;
  });
};
```
