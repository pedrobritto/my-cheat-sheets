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
async function updateCourse(id, data) {
  try {
    const course = await Course.findByIdAndUpdate(
      id,
      {
        $set: data
      },
      { new: true }
    );
    console.log(course);
  } catch (err) {
    console.log(err);
  }
}

updateCourse(id, {
  author: "Pedro",
  isPublished: false
});
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

We want to associate an author posts to a course, so we initialize an `author` field inside `courseSchema`.

This method will embed the full content of `author` inside a course document.

```js
const authorSchema = new mongoose.Schema({
  name: String,
  bio: String,
  website: String
});

const courseSchema = new mongoose.Schema({
  name: String,
  author: authorSchema
});

const Author = mongoose.model("Author", authorSchema);
const Course = mongoose.model("Course", courseSchema);
```

The author subdocument can be upadted inside the course document.

```js
async function updateAuthor(courseId, authorName) {
  await Course.findById(courseId);
  course.author.name = authorName;
  course.save();
}
```

Query first can also be used.

```js
async function updateAuthor(courseId, authorName) {
  await Course.update(
    { _id: courseId },
    {
      $set: {
        "author.name": authorName
      }
    }
  );
}
```

To remove a subdocument, use the `$unset` operator.

```js
async function updateAuthor(courseId, authorName) {
  await Course.update(
    { _id: courseId },
    {
      $unset: {
        "author.name": ""
      }
    }
  );
}
```

We can add new posts to existing users.

```js
// TODO: Needs refactoring
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
```

### Embedding - Arrays

Using a subdocument array is very similar to using a single subdocument.

```js
const authorSchema = new mongoose.Schema({
  name: String,
  bio: String,
  website: String
});

const courseSchema = new mongoose.Schema({
  name: String,
  authors: [authorSchema]
});

const Author = mongoose.model("Author", authorSchema);
const Course = mongoose.model("Course", courseSchema);

// Creating a new course
async function createCourse(name, authors) {
  const course = new Course({
    name,
    authors
  });

  const result = await course.save();
}

createCourse("Course Name", [
  new Author({ name: "author 1" }, new Author({ name: "author 2" }))
]);
```

To add a new subdocument:

```js
async function addAuthor(courseId, author) {
  const course = await Course.findById(courseId);
  course.authors.push(author);
  course.save();
}
```

And to remove:

```js
async function removeAuthor(courseId, authorId) {
  const course = await Course.findById(courseId);
  const author = await course.authors.id(authorId);
  author.remove();
  course.save();
}
```

### Object References

This will reference the ID of a document inside another document. On run time, the data can be populated.

```js
const Author = mongoose.model(
  "Author",
  new mongoose.Schema({
    name: String,
    bio: String,
    website: String
  })
);

const Course = mongoose.model(
  "Course",
  new mongoose.Schema({
    name: String,
    author: {
      type: mongoose.Schema.Types.ObjectId, // Need to have ObjectId type
      ref: "Author" // name of referenced collection
    }
  })
);
```

```js
// Helper function to create author
async function createAuthor(name, bio, website) {
  const author = new Author({
    name,
    bio,
    website
  });

  const result = await author.save();
}

// Helper function to create course
async function createCourse(name, author) {
  const course = new Course({
    name,
    author
  });

  const result = await course.save();
}

createAuthor("Mosh", "My bio", "My Website");
createCourse("Node Course", "5b94e7435720aa2beba8cbbb"); // ID of created author
```

A query for all courses would return:

```json
[
  {
    "_id": "5b94e896f85376331bb3821b",
    "name": "Node Course",
    "author": "5b94e7435720aa2beba8cbbb"
  }
]
```

We can then use the author ID inside the `author` array to populate it during runtime by using `.populate()`.

```js
// Helper function to list courses
async function listCourses() {
  const courses = await Course.find()
    .populate("author", "name -_id")
    .select("name author");
}
```

The second argument receives a `.select()` string, this will return only the author name.

```json
[
  {
    "_id": "5b94e896f85376331bb3821b",
    "name": "Node Course",
    "author": {
      "name": "Mosh"
    }
  }
]
```

Note that non-existants documents can be referenced inside another. When that happens, the populated value will be `null`.
