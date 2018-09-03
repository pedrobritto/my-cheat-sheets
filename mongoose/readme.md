# My Mongoose Cheat Sheet

## Instalation

```
npm i mongoose
```

## Usage

### Requiring

```js
const mongoose = require("mongoose");
```

### Connecting to DB

```js
mongoose.connect(
  "mongodb://localhost:27017/DB_NAME",
  { useNewUrlParser: true }
);
```

### Creating a Schema

```js
const exampleSchema = new mongoose.Schema({
  title: String,
  date: String,
  value: Number
});
```

### Initializing a model/collection

```js
const Example = mongoose.model("Example", exampleSchema);
```

This example will create a collection called "examples" (plural) from the string `"Example"` passed as the first argument of the `model()` and take in a `mongoose.Schema()` as the second argument.

The variable `Example` can then be used to query inside the `examples` collection inside the `DB_NAME`.

## Database Operations

### Creating a collection item

```js
Example.create(
  {
    title: req.body.title,
    date: req.body.date,
    value: req.body.value
  },
  (err, item) => {
    if (err) {
      res.json(err);
    } else {
      res.json(item);
    }
  }
);
```

`Model.create({ key: "value" })` will create a new item inside the collection with the givem key-values. Callback is used for error handling and returning the result.

### Returning all collection items

```js
Example.find({}, (err, item) => {
  if (err) {
    res.json(err);
  } else {
    res.json(item);
  }
});
```

`Model.find({})` will return all items inside a collection.

### Return a collection item by ID

```js
Example.findById(id, (err, item) => {
  if (err) {
    res.json(err);
  } else {
    res.json(item);
  }
});
```

`Model.findById(id)` will return the item with the passed id.

### Find a collection item by ID and update

```js
Example.findByIdAndUpdate(id, newValue, (err, item) => {
  if (err) {
    res.json(err);
  } else {
    res.json(item);
  }
});
```

`Model.findByIdAndUpdate(id, newValue)` will find an item by id and then replace it's values with the newValue object.

### Find a collection item by ID and remove

```js
Example.findByIdAndRemove(id, (err, item) => {
  if (err) {
    res.json(err);
  } else {
    res.json(item);
  }
});
```

`Model.findByIdAndRemove(id)` will find an item by id and them remove it.

## Data associations

### Embedding

We want to associate many posts to one user, so we initialize a `posts` array inside the `userSchema`.

This method will embed the full content of `posts` inside the user document.

```js
const postSchema = new mongoose.Schema({
  title: String,
  content: String
});

const userSchema = new mongoose.Schema({
  name: String,
  email: String,
  posts: [postSchema]
});

PostModel = mongoose.model("Post", postSchema);
UserModel = mongoose.model("User", userSchema);
```

We can add new posts to existing users.

```js
UserModel.findOne({ name: "User Name" }, (err, user) => {
  if (err) console.log(err);
  else {
    // Push new post to user's array
    user.posts.push({
      title: "Post title",
      content: "Post content"
    });

    // Save modified user to DB
    user.save((err, data) => {
      if (err) console.log(err);
      else console.log(data);
    });
  }
});

// Alternative syntax
// Not tested!
UserModel.update(
  { _id: id },
  {
    $push: {
      posts: {
        title: "Post title",
        content: "Post content"
      }
    }
  }
);
```

### Object References

This will reference the ID of a document inside another document. On run time, the data can be populated.

```js
const postSchema = new mongoose.Schema({
  title: String,
  content: String
});

const userSchema = new mongoose.Schema({
  name: String,
  email: String,
  posts: [
    {
      type: mongoose.Schema.Types.ObjectId,
      ref: "Post" // Collection?
    }
  ]
});

PostModel = mongoose.model("Post", postSchema);
UserModel = mongoose.model("User", userSchema);
```

```js
Post.create(
  {
    title: "A post",
    content: "The post content"
  },
  (err, post) => {
    if (err) {
      console.log(err);
    } else {
      // Finds user
      User.findOne({ email: "bob@email.com" }, (err, user) => {
        if (err) {
          console.log(err);
        } else {
          // Add and save the post reference to the user's posts array
          user.posts.push(post);
          user.save((err, data) => {
            if (err) {
              console.log(err);
            } else {
              console.log(data);
            }
          });
        }
      });
    }
  }
);
```

A query for "bob@email.com" would return:

```json
{
  "posts": ["5b8bc98ee4f3d30f7db60d64", "5b8bca45789ab70fcd7e6259"],
  "_id": "5b8bc801afe0c60f4e34e6bd",
  "email": "bob@email.com",
  "name": "Bob",
  "__v": 2
}
```

We can then use the post ID's inside the `posts` array to populate it during runtime.

```js
User.findOne({ email: "bob@email.com" })
  .populate("posts")
  .exec((err, data) => {
    if (err) {
      console.log(err);
    } else {
      console.log(data);
    }
  });
```

It'll return:

```json
{
  "posts": [
    {
      "_id": "5b8bc98ee4f3d30f7db60d64",
      "title": "A post, pt 2",
      "content": "qwer qwer qwer",
      "__v": 0
    },
    {
      "_id": "5b8bca45789ab70fcd7e6259",
      "title": "A post, pt 3",
      "content": "zxcv zxcv zxcv",
      "__v": 0
    }
  ],
  "_id": "5b8bc801afe0c60f4e34e6bd",
  "email": "bob@email.com",
  "name": "Bob",
  "__v": 2
}
```

But querying for "bob@email.com" again would return the same result as before, object references only.
