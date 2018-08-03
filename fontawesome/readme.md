## Usando Font Awesome 5 em pseudo-elementos

source: https://stackoverflow.com/questions/47712987/font-awesome-5-on-pseudo-elements

```scss
.class::before {
  content: "\f16c"; // Symbol you want to use

  font-family: "Font Awesome 5 Free"; // for Regular and Solid symbols.
  font-family: "Font Awesome 5 Brands"; // for Brands symbols.

  font-weight: 900; // for Regular and Brands symbols.
  font-weight: 400; // for Solid symbols.
}
```
