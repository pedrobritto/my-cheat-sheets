# My JavaScript Cheat Sheet

## Sorting

### Sort ascending

```js
toSort = [4, 2, 3, 1];

toSort.sort();

// Or
toSort.sort((a, b) => {
  return a > b;
});

// Will print [1, 2, 3, 4]
```

### Sort descending

```js
toSort = [4, 2, 3, 1];

toSort.sort((a, b) => {
  return a < b;
});

// Will print [4, 3, 2, 1]
```

### Sorting strings

```js
const toSort = ["c", "b", "a"];

toSort.sort();

// Or
toSort.sort((a, b) => {
  return a > b;
});
```

### Sorting Objects inside Array by a key value

```js
const toSort = [{ prop: "c" }, { prop: "b" }, { prop: "a" }];

toSort.sort((a, b) => {
  return a.prop > b.prop;
});
```

### `Element.offsetWidth`, `Element.clientWidth`, `Element.scrollWidth` e `window.innerWidth`

- `Element.offsetWidth`, `Element.offsetHeight`: The size of the visual box **INCLUDING** all borders. Can be calculated by adding width/height and paddings and borders, if the element has `display: block`.
- `Element.clientWidth`, `Element.clientHeight`: The visual portion of the box content, **NOT INCLUDING** borders or scroll bars, but **INCLUDES** padding . Can not be calculated directly from CSS, depends on the system's scroll bar size.
- `Element.scrollWidth`, `Element.scrollHeight`: The size of all of the box's content, including the parts that are currently hidden outside the scrolling area. Can not be calculated directly from CSS, depends on the content.

> `window.innerWidth` e `window.innerHeight` parecem funcionar de forma semelhante ao `clientWidth` e `clientHeight`, mas para a viewport.

### Document scroll position

```js
// Chrome, Firefox
document.documentElement.scrollTop;

// Safari
document.body.scrollTop;
```

### Event delegation

```js
document.body.addEventListener("click", event => {
  const targetElement = event.target.closest(".element");

  if (!targetElement) {
    // do something!
  }
});
```

### Access content inside `<object>` or `<iframe>`

```js
iframe = document.querySelector("#iframe-id");
iframeContent = iframe.contentDocument;

// Use iframeContent as a Ref
iframeContent.querySelector(".inside-iframe");
```

### `window.location` Object

- Global object: `window.location`;
- Change url: `window.location.assign(url: string)`;
- Open url in a new window: `window.open(url: string)`;

### Creating and appending an element to DOM

```js
const targetContainer = document.querySelector(".target-container");

function createElement() {
  const fragment = document.createRange().createContextualFragment(
    `<div class="parent-class">
      <div class="child-class">
        Some stuff in here.
      </div>
    </div>`
  );

  targetContainer.appendChild(fragment);
}
```

> To append the same element several times, use a loop and call the `createElement()` function inside it. If you try to append the fragment inside the `createElement()` function, it'll work only once.

### Google maps

```html
<div id="map">
  <script type="text/javascript">
    function initMap() {
      var myLatLng = {
        lat: -3.7381046,
        lng: -38.4943983
      };
      var map = new google.maps.Map(document.getElementById("map"), {
        zoom: 16,
        center: myLatLng
      });
      var marker = new google.maps.Marker({
        position: myLatLng,
        map: map,
        title: "Ouvidor"
      });
    }
  </script>

  <script
    async
    defer
    src="https://maps.googleapis.com/maps/api/js?key=API_KEY&callback=initMap"
  ></script>
</div>
```
