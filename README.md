tldr. dont use this, just use tailwind

# Scss utils

A collection of scss utils for management of project styles and variables.

## Installation

```sh
npm i @lbagic/scss-utils
```

**_Overview:_**

| name                                              | type     | description                                                          |
| ------------------------------------------------- | -------- | -------------------------------------------------------------------- |
| [generate-css-variables](#generate-css-variables) | mixin    | generate native css variables from scss maps                         |
| [get-color-palette](#get-color-palette)           | function | create color palette from color & variant definitions                |
| [get-json](#get-json)                             | function | create json from scss structures for convenient export to javascript |
| [map-deep-get](#map-deep-get)                     | function | util for accessing (deep) map keys                                   |
| [map-flatten](#map-flatten)                       | function | util for flattening nested scss maps                                 |
| [normalize](#normalize)                           | styles   | useful noramlized styles                                             |

---

## generate-css-variables

_Type: mixin  
Signature: `generate-css-variables($map, $prefix: null, $skip-key: null)`_

Mixin for converting scss maps to native css variables.

Example:

```scss
// variables.scss
$prefix: 'google'
$project-variables: (
  "color": (
    "primary": #007bff,
    "accent": #f92f80,
  ),
  "breakpoint": (
    "s": 640px,
    "m": 768px,
  ),
  "deeply": (
    "nested": (
      "prop": 1,
    ),
  ),
);
```

```scss
// index.scss
:root {
  --prefix: #{$prefix};
  @include generate-css-variables($project-variables, $prefix);
  // --google-color-primary: #007bff;
  // --google-color-accent: #f92f80;
  // --google-breakpoint-s: 640px;
  // --google-breakpoint-m: 768px;
  // --google-deeply-nested-prop: 1;
}
```

---

## get-color-palette

_Type: function  
Signature: `get-color-palette($colors, $variants)`  
Hint: uses [scale-color](https://sass-lang.com/documentation/modules/color#scale) under the hood for defining variants_

Function for defining color palette with different shades, variants, and respective contrasts.

Example:

```scss
$colors: (
  "primary": #007bff,
  "accent": #f92f80,
);
$variants: (
  "light": (
    "lightness": 8%,
  ),
  "opaque": (
    "saturation": 50%,
    "lightness": 95%,
    "alpha": -50%,
  ),
);

$color-palette: get-color-palette($colors, $variants);
// (
//   "primary": (
//     "base": #007bff,
//     "base-contrast": #ffff,
//     "light": #1486ff,
//     "light-contrast": #ffff,
//     "opaque": rgba(242, 248, 255, 0.5),
//     "opaque-contrast": #000,
//   ),
//   "accent": (
//     "base": #f92f80,
//     "base-contrast": #ffff,
//     "light": #f9408a,
//     "light-contrast": #000,
//     "opaque": rgba(255, 244, 249, 0.5),
//     "opaque-contrast": #000,
//   ),
// );
```

---

## get-json

_Type: function  
Signature: `get-json($structure)`  
Hint: Extending css.js with type definitions goes a long way._

Function for converting scss structures to json string. Great tool for keeping a single source of truth in scss and propagating it to js.

Example:

```scss
// variables.scss
$prefix: "google";
$project-variables: (
  "color": (
    "primary": #007bff,
    "accent": #f92f80,
  ),
  "breakpoint": (
    "s": 640px,
    "m": 768px,
  ),
  "deeply": (
    "nested": (
      "prop": 1,
    ),
  ),
);
```

```scss
// export.module.scss
:export {
  prefix: $prefix;
  jsonCssVariables: get-json($project-variables);
}
```

```js
// css.js
import { prefix, jsonCssVariables } from "../export.module.scss";

export const css = {
  prefix,
  ...JSON.parse(jsonCssVariables.slice(1, -1)),
};
// {
//   prefix: "google",
//   color: { primary: "#007bff", accent: "#f92f80" },
//   breakpoint: { s: "640px", m: "768px" },
//   deeply: { nested: { prop: "1" } },
// };
```

---

## map-deep-get

_Type: function  
Signature: `map-deep-get($map, $keys...)`_

Function for easier access to map values.

Example:

```scss
// variables.scss
$project-variables: (
  "breakpoint": (
    "s": 640px,
    "m": 768px,
  ),
);

@function get-project-variables($keys...) {
  @return map-deep-get($project-variables, $keys...);
}
```

```scss
// breakpoint-mixins.scss
@mixin s {
  @media screen and (min-width: get-project-variables("breakpoint", "s")) {
    @content;
  }
}

@mixin m {
  @media screen and (min-width: get-project-variables("breakpoint", "m")) {
    @content;
  }
}
```

---

## map-flatten

_Type: function  
Signature: `map-flatten($map, $delimiter: "-", $skip-key: null, $prefix: null)`_

Function for flattening nested scss maps. Used internally in [generate-css-variables](#generate-css-variables) mixin. Potentially has more usecases so it is exported as a standalone function.

Example:

```scss
$project-variables: (
  "breakpoint": (
    "s": 640px,
    "m": 768px,
  ),
);

$flat: map-flatten($project-variables);
// (
//   "breakpoint-s": 640px;
//   "breakpoint-m": 768px;
// );
```

---

## normalize

_Type: styles
Hint: experimental_

Basic normalized styles.
