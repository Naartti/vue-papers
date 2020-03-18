# vue-papers
## Installation
```
npm install vue-papers
```

## Usage
```html
<vue-papers
  :margin-between="10"
  :padding-top="20"
  :padding-bottom="20"
  >

  <!-- Optional secondary slots (header & footer) -->
  <div slot="header">Header on every page</div>
  <div slot="footer">Footer on every page</div>

  <!-- Elements in default slot will be placed on to A4 papers. Page breaks will occure between elements. -->
  <p>Lorem ipsum dolor sit amet, consectetur adipisci elit, sed eiusmod tempor incidunt ut labore et dolore magna aliqua.</p>
  <p>Lorem ipsum dolor sit amet, consectetur adipisci elit, sed eiusmod tempor incidunt ut labore et dolore magna aliqua.</p>

</vue-papers>
```

## Props
|props|type|default|note|
|---|---|---|---|
|`margin-between`|number|0|Distance between elements in main slot.|
|`padding-top`|number|0|Page padding (top)|
|`padding-bottom`|number|0|Page padding (bottom)|