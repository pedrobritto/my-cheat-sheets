Detecta mudanças em apenas um arquivo:

```js
gulp.watch("app/template/**/*", obj => {
  const savedFilePath = obj.path.split("/");
  const savedFileName = savedFilePath[savedFilePath.length - 1];

  console.log("Copying saved .php file to dist", `(${savedFileName})`);

  if (obj.type === "changed") {
    gulp
      .src(obj.path, { base: path.resolve(__dirname, "src", "template") })
      .pipe(gulp.dest(path.resolve(__dirname, "dist")));
  }
});
```
