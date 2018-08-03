Desativa swiper se houver apenas um slide:

```js
new Swiper(".slider-home", {
  // ...
  on: {
    init: function() {
      if (this.slides.length == 1) {
        $(".swiper-pagination").hide();
      }
    }
  }
});
```
