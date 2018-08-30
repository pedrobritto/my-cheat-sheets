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

### Acessar conteúdo de um `<object>` ou `<iframe>`

```js
iframe = document.querySelector("#iframe-id");
iframeContent = iframe.contentDocument;

// Referenciar com iframeContent
iframeContent.querySelector(".inside-iframe");
```

### Objeto `window.location`

- Objeto global: `window.location`;
- Mudar url da página atual: `window.location.assign(url: string)`;
- Abrir uma nova janela: `window.open(url: string)`

### Google maps

```html
<div id="map">

<script type="text/javascript">
function initMap() {
    var myLatLng = {
        lat: -3.7381046,
        lng: -38.4943983
    };
    var map = new google.maps.Map(document.getElementById('map'), {
        zoom: 16,
        center: myLatLng
    });
    var marker = new google.maps.Marker({
        position: myLatLng,
        map: map,
        title: 'Ouvidor'
    });
}
</script>

<script async defer src="https://maps.googleapis.com/maps/api/js?key=API_KEY&callback=initMap"></script>
```
