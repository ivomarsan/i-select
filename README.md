<p align="center">
  <a href="http://ivomarsan.com/" target="_blank">
    <img width="128" src="http://ivomarsan.com/favicon.png">
  </a>
</p>

<p align="center">i-select</p>

<p align="center">

  <a href="https://www.npmjs.com/package/i-select">
    <img src="https://img.shields.io/npm/dt/i-select.svg" alt="Downloads">
  </a>

  <a href="https://www.npmjs.com/package/i-select">
    <img src="https://img.shields.io/npm/v/i-select.svg" alt="Version">
  </a>

  <a href="https://www.npmjs.com/package/i-select">
    <img src="https://img.shields.io/npm/l/i-select.svg" alt="License">
  </a>
</p>

---

<a href="https://www.npmjs.com/package/i-select">`i-select`</a> is a
simplistic select with support to multiselect for VueJS.

## Installation

Install via `npm`

```bash
npm install i-select --save
```

Import or require in your code:

```javascript
import Vue from 'vue';
import iSelect from 'i-select';

// OR

var Vue = require('vue');
var iSelect = require('i-select');
```

### Module

```javascript
import iSelect from 'i-select';

// ...

export default {
  // ...
  components: {
    'my-awesome-select': iSelect,
  },
  // ...
};
```

### Browser

```html
<script src="https://cdn.jsdelivr.net/npm/vue"></script>
<script src="dist/i-select.min.js"></script>
<script>
Vue.use(iSelect);

new Vue({
  el: '#app'
});
</script>
```

## Usage

It's very useful to use `i-select` you only need to register then :smile:
seems like with

```html
<i-select v-model="select" :options="['A', 'B', 'C']">

</i-select>
```

It's easier to use, you only need to pass an `array []` with `Number` or
`String` type. When you pass an `Object` you need some attention to default key
`label`. Some like this:

```html
<i-select v-model="select" :options="options">

</i-select>
```

```javascript
import iSelect from 'i-select';

export default {
  components: {
    iSelect,
  },
  data: () => ({
    select: '',
    options: [
      { label: 'Option 1', hide: `you'll don't see this text` },
      { label: 'Option 2', hide: `you'll don't see this text` },
      { label: 'Option 3', hide: `you'll don't see this text` },
      { label: 'Option 4', hide: `you'll don't see this text` },
    ],
  }),
};
```

But you can personalize this with some properties like

#### Options

> @type {Array}
> @default []

Options to show on select. You probably need to use a `v-model` to receive
this data information.

```html
<i-select v-model="myModel" :options="['A', 'B', 'C']">
</i-select>
```

#### Label

> @type {String}
> @default 'label'

Imagine now, you have `myOptions` that is an Array but you haven't a key
property like `label`. Are you thinking to use an `array.map()`? No way! You
should to use `label property` passing the key name that you want use instead.

```html
<i-select v-model="myModel" :options="myOptions" label="name">
</i-select>
```

#### Filter

> @type {Boolean}
> @default false

When `true` a input search will be avaliable to filter results in `options` attribute.

```html
<i-select filter v-model="myModel" :options="options">
</i-select>
```

#### Multiple

> @type {Boolean}
> @default false

Attribute `multiple` return an `array` of results instead a single result selected.

```html
<i-select multiple v-model="myModel" :options="options">
</i-select>
```

#### Initial value

> @type {String}
> @default ''

Will be impressed instead _i-select_

```html
<i-select initial="Hi I'm Goku">
</i-select>
```

#### Placeholder

> @type {String}
> @default ''

Add a placeholder on filter when avaliable

```html
<i-select placeholder="Search here">
</i-select>
```

#### No Matching

> @type {String}
> @default 'Sorry, no matching options.'

Alternative message to show when has no matching on filter

```html
<i-select no-matching="Sorry :/">
</i-select>
```

#### Return

> @type {String}

Return to `v-model` only a single value instead an object. Enter with key name and get this value

```html
<i-select return="id">
</i-select>
```

#### Limit

> @type {String}
> @default '5'

Limit how much results will be avaliable in Select.

```html
<i-select limit="8">
</i-select>
```

#### Disabled

> @type {Boolean}
> @default false

Disable the entire component

```html
<i-select disabled>
</i-select>
```

#### Hide Results

> @type {Boolean}
> @default false

Hide results from Select when filter is called

```html
<i-select hide-results>
</i-select>
```

#### Hide Label

> @type {Boolean}
> @default false

Hide Label from `<input>`

```html
<i-select hide-label>
</i-select>
```

#### Max Height

> @type {String}
> @default '200px'

Sets the max-height property on the select list.

```html
<i-select max-height="500px">
</i-select>
```

## Personalization

You can personalize your `<i-select>` with class `i-select` or through some
properties.

#### Uppercase

> @type {Boolean}
> @default false

Convert All Exibed Text to UPPERCASE

```html
<i-select uppercase>
</i-select>
```

#### isBackground

> @type {String}

Paint Background Color

```html
<i-select is-background="#ff0"></i-select>
```

#### isOutline

> @type {String}

Paint Border Color

```html
<i-select is-outline="#f0f"></i-select>
```

#### isColor

> @type {String}

Paint Text Color

```html
<i-select is-color="#00f"></i-select>
```

#### isTooltip

> @type {String}

Give us a Tooltip

```html
<i-select is-tooltip="This is a Tooltip"></i-select>
```

This example will open a `Tooltip` with a message to `top`. See above all
tooltips positions (`is-position`) avaliable:

- `top`
- `left`
- `right`
- `bottom`

```html
<i-select is-tooltip="This is a Tooltip" is-position="right"></i-select>
```

## Demo

[JSFiddle](https://jsfiddle.net/c69xrqfe/)
