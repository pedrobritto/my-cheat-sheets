## Immutability

### Changing an array the right way

```js
const increment = (list, index) => {
  if (index >= list.length) {
    return list;
  }
  return [...list.slice(0, index), list[index] + 1, ...list.slice(index + 1)];
};

const addItem = (list, item) => {
  return [...list, item];
};

const removeItem = (list, index) => {
  return [...list.slice(0, index), ...list.slice(index + 1)];
};
```

### Changing an object the right way

```js
const obj = { text: "Hello" };
const newObj = Object.assign({}, { anotherText: "there" });

// or

const newObj = {
  ...obj,
  anotherText: "there"
};
```
