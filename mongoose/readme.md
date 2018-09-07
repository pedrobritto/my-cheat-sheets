# My Mongoose Cheat Sheet

## Instalation

```
npm i mongoose
```

## Usage

### Requiring

```js
const mongoose = require("mongoose");
const Schema = mongoose.Schema;
```

### Connecting to DB

```js
mongoose
  .connect(
    "mongodb://localhost:27017/DB_NAME",
    { useNewUrlParser: true }
  )
  .then(() => console.log("Connected to MongoDB"))
  .catch(err => console.log(err));
```

### Creating a Schema

```js
const courseSchema = Schema({
  name: String,
  author: String,
  tags: [String],
  date: {
    type: Date,
    default: Date.now
  },
  isPublished: Boolean
});
```

Schema types:

```
String
Number
Date
Buffer
Boolean
Mixed
ObjectId
Array
Decimal128
Map
```

### Compiling a Document Model

```js
const Example = mongoose.model("Example", exampleSchema);
```

This example will create a collection called "examples" (plural) from the string `"Example"` passed as the first argument of the `model()` and take in a `mongoose.Schema()` as the second argument.

The constant `Example` can then be used to query and operate inside the `examples` collection inside the `DB_NAME`.

## Database Operations

### Creating a document in collection

```js
async function createCourse(name, author, tags, isPublished) {
  try {
    const course = new Course({
      name: name,
      author: author,
      tags: tags,
      isPublished: isPublished
    });

    const result = await course.save();
    console.log(result);
  } catch (err) {
    console.log(err);
  }
}

createCourse(args...);
```

`Model.create({ key: "value" })` will create a new item inside the collection with the given key-values. Catch is used for error handling.

### Find all documents in collection

```js
async function find() {
  try {
    const courses = await Course.find();
    console.log(courses);
  } catch (err) {
    console.log(err);
  }
}

find();
```

`Model.find({})` will return all items inside a collection.

### Find a document in collection by ID

```js
async function findById(id) {
  try {
    const course = await Example.findById(id);
    console.log(course);
  } catch (err) {
    console.log(err);
  }
}

findById(OBJECT_ID);
```

`Model.findById(id)` will return the item with the passed id.

### Advanced querying

```js
async function advancedQuery() {
  try {
    const courses = await Course.find({ isPublished: { $in: [true, false] } })
      .sort({ name: 1 }) // sort ascending by name
      .select({ name: 1, tags: 1 }) // display only name and tags
      .count(); // return number of documents that match query
    console.log(courses);
  } catch (err) {
    console.log(err);
  }
}

advancedQuery();
```

### Update document - Query First

```js
async function updateCourse(id) {
  try {
    const course = await Course.findById(id);
    if (!course) return;

    const result = await course
      .set({
        isPublished: true,
        author: "Another author"
      })
      .save();

    console.log(result);
  } catch (err) {
    console.log(err);
  }
}

updateCourse(OBJECT_ID);
```

### Update document - Update First

You can either return the status of the change:

```js
async function updateCourse(id) {
  try {
    const updateOperationResult = await Course.update(
      { _id: id },
      {
        $set: {
          author: "Pedro",
          isPublished: false
        }
      }
    );
    console.log(updateOperationResult);
  } catch (err) {
    console.log(err);
  }
}
```

Or you can return the updated object:

```js
async function updateCourse(id) {
  try {
    const course = await Course.findByIdAndUpdate(
      id,
      {
        $set: {
          author: "Pedro",
          isPublished: false
        }
      },
      { new: true }
    );
    console.log(course);
  } catch (err) {
    console.log(err);
  }
}
```

`Model.findByIdAndUpdate(id, newValue)` will find an item by id and then replace it's values with the newValue object.

### Find and remove a document

Returns status of document removal:

```js
async function removeCourse(id) {
  try {
    const result = await Course.deleteOne({ _id: id });
    console.log(result);
  } catch (err) {
    console.log(err);
  }
}
```

Or to return the removed document:

```js
async function removeCourse(id) {
  try {
    const course = await Course.findByIdAndRemove(id);
    console.log(course);
  } catch (err) {
    console.log(err);
  }
}
```

### Remove several documents

```js
async function removeCourse(id) {
  try {
    const result = await Course.deleteMany({ isPublished: false });
    console.log(result);
  } catch (err) {
    console.log(err);
  }
}
```

This deletes all documents with `{ isPublished: false }`.

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
