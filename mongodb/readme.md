# My MongoDB Cheat Sheet

## Instalation

Soon.

## As a docker container

```
docker run -d -p 27017:27017 -v ~/data:/data/db mongo:4.1
```

Then connect (via mongoose):

```js
const mongoose = require("mongoose");

mongoose
  .connect("mongodb://0.0.0.0:27017/mydb")
  .then(() => console.log("Connected to MongoDB"))
  .catch(err => console.log(err));
```

The `"mongodb://0.0.0.0:27017/mydb"` can be replaced by `"mongodb://localhost:27017/mydb"`.

## Basic operations

### Enable shell

```
mongo
```

### Show all dbs

```
> show dbs
```

### Create or use db

```
> use <db_name>
```

### Show all collections inside db

```
> show collections
```

Use when inside a db.

### Add item to collection

```
> db.<collection_name>.insert({ prop: "value"})
```

### Show all items in collection

```
> db.<collection_name>.find()
```

### Show all items with specific value in collection

```
> db.<collection_name>.find({ prop: "value" })
```

### Update item (all fields)

```
> db.<collection_name>.update(<search>, <replace>)
> db.<collection_name>.update({ prop: "value" }, { prop: "new value" })
> db.<collection_name>.update({ prop: "value" }, { newProp: "new value" })
```

### Update item (given fields only)

```
> db.<collection_name>.update(<search>, { $set: <replace> })
> db.<collection_name>.update({ prop: "value" }, { $set: { prop: "new value" }})
> db.<collection_name>.update({ prop: "value" }, { $set: { newProp: "new value" }})
```
