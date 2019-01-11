# vue-active-swiper

![NPM](https://nodei.co/npm/vue-active-swiper.png?downloads=true&downloadRank=true&stars=true)

![img](https://img.shields.io/npm/v/vue-active-swiper.svg) ![img](https://img.shields.io/bundlephobia/minzip/vue-active-swiper.svg) ![img](https://img.shields.io/npm/dt/vue-active-swiper.svg) ![img](https://img.shields.io/github/license/accforgit/vue-active-swiper.svg)

`vue-active-swiper` is a Mobile-oriented、No dependencies、Lightweight Swiper component for Vue

![img](https://raw.githubusercontent.com/accforgit/vue-active-swiper/master/public/swiper-1.gif)

[简体中文](https://github.com/accforgit/vue-active-swiper/blob/master/README.md) | English

## Example

- [example - basic](https://accforgit.github.io/vue-active-swiper/basic.html)

## Install

```
npm install vue-active-swiper --save
```

## Import

### import with global

```js
// require styles
import 'vue-active-swiper/dist/VueActiveSwiper.css'

import Vue from 'vue'
import VueActiveSwiper from 'vue-active-swiper'

Vue.use(VueActiveSwiper)
```

### import with component

```js
// require styles
import 'vue-active-swiper/dist/VueActiveSwiper.css'

// in ES6 modules
import { Swiper, SwiperItem } from 'vue-active-swiper'

// in CommonJS
const { Swiper, SwiperItem } = require('vue-active-swiper')

export default {
  components: {
    Swiper,
    SwiperItem
  }
}
```

### import with script

```html
<link rel="stylesheet" href="../node-modules/vue-active-swiper/dist/VueActiveSwiper.css" charset="utf-8">
<script src="../node-modules/vue-active-swiper/dist/VueActiveSwiper.umd.min.js"></script>
```

```js
const Swiper = VueActiveSwiper.Swiper
const SwiperItem = VueActiveSwiper.SwiperItem

new Vue({
  el: '#app',
  components: {
    Swiper,
    SwiperItem
  }
})
```

## Usage

Work on a Vue instance:
```html
<Swiper>
  <SwiperItem>1</SwiperItem>
  <SwiperItem>2</SwiperItem>
  <SwiperItem>3</SwiperItem>
</Swiper>
```
`or`
```html
<Swiper :urlList="[
  'https://dummyimage.com/375x100/FB8A80?text=1',
  'https://dummyimage.com/375x100/29A90F?text=2',
  'https://dummyimage.com/375x100/6F9DFF?text=3'
]" />
```

## Options

### Props

|Option|Type|Description|Default|necessary|
|----|---|----|----|---|
|urlList|Array|image array <br>If specified, subcomponents(`SwiperItem`) passed in by components will not work,<br> Either `urlList` or `SwiperItem`，If both exist at the same time，`urlList` shall prevail|null|false|
|backgroundSize|String|`Specifies how the image is scaled in the sliding-container-box`(`Swiper`)，Value and Effect are the same as `CSS background-size` <br>Valid only when urlList is specified|cover|false|
|clientW|Number|Width of the sliding-container-box (`Swiper`)|`document.documentElement.clientWidth`|false|
|clientH|Number|Height of the sliding-container-box (`Swiper')|200|false|
|showCounter|Boolean|if need a default counter|false|false|
|counterStyle|Object|Customize the style of the default counter <br>Valid only when `showCounter` is `true`|null|false|
|startIndex|Number|Start swipe item index|0|false|
|criticalValue|Number|Proportional value of critical point <br>When it exceeds the critical point represented by this value, it will automatically slide to the next picture.|1/3|false|
|autoPlayDelay|Number|If this parameter is specified and the value `>= 0`, the value will be taken as the time of automatic rotation `delay`(`ms`) for automatic rotation;Non-designated non-automatic rotation <br>If you want to specify this value, it is generally recommended to set it to `3000`|null|false|
|duration|Number|The time(`ms`) required to automatically scroll to a stable position|350|false|
|noDragWhenSingle|Boolean|If there is only one `swipeItem`, is dragging prohibited|true|false|

### Events

|Event Name|Description|params|
|---|---|---|
|click|Click events for the component|activeIndex|
|change|Callback after each scroll|activeIndex|

## Extra

### goto

The `Swiper`component provides a method `goto`, which allows users to specify slide to which `Swiper Item`:

```js
this.$refs.mySwiper.goto(2)
```
`goto` receives a `Number` parameter(`index`),eepresents sliding to `a Swiper Item` with `index` subscript 
`index` start at `0`

see more [example - basic-slot](https://github.com/accforgit/vue-active-swiper/blob/master/example/basic-slot.vue)

### Named Slot

`Swiper` can also receive a named slot - `extra`，make it easier for developers to customize components more freely:

```html
<Swiper>
  <SwiperItem>1</SwiperItem>
  <SwiperItem>2</SwiperItem>
  <SwiperItem>3</SwiperItem>
  <!-- named slot -->
  <p slot="extra">custom extra content</p>
</Swiper>
```

## License

MIT