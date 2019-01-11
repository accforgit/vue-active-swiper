# vue-active-swiper

![NPM](https://nodei.co/npm/vue-active-swiper.png?downloads=true&downloadRank=true&stars=true)

![img](https://img.shields.io/npm/v/vue-active-swiper.svg) ![img](https://img.shields.io/bundlephobia/minzip/vue-active-swiper.svg) ![img](https://img.shields.io/npm/dt/vue-active-swiper.svg) ![img](https://img.shields.io/github/license/accforgit/vue-active-swiper.svg)

`vue-active-swiper` 是一个面向移动端、无依赖、轻量级的 `vue`轮播组件

![img](https://raw.githubusercontent.com/accforgit/vue-active-swiper/master/public/swiper-1.gif)

[English](https://github.com/accforgit/vue-active-swiper/blob/master/README_US.md) | 简体中文

## 示例

- [example - basic](https://accforgit.github.io/vue-active-swiper/basic.html)

## 安装

```
npm install vue-active-swiper --save
```

## 导入

### 全局导入

```js
// 样式引用
import 'vue-active-swiper/dist/VueActiveSwiper.css'

import Vue from 'vue'
import VueActiveSwiper from 'vue-active-swiper'

Vue.use(VueActiveSwiper)
```

### 局部导入

```js
// 样式引用
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

### script 脚本形式导入

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

## 用法

在 `Vue`组件实例中使用:
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

|参数|类型|描述|默认值|是否必须|
|----|---|----|----|---|
|urlList|Array|传入的图片数组 <br>如果指定了此值，则 `Swiper`组件传入的 `SwiperItem`子组件将不起作用，<br>`urlList` 与 `SwiperItem`二选一，如果二者同时存在，优先以 `urlList`为准|null|否|
|backgroundSize|String|图片以何种缩放的形式铺在滑动容器框(`Swiper`)内，取值及效果都与 `CSS background-size`一致 <br>只有当指定了 urlList 时才有效|cover|否|
|clientW|Number|滑动容器框(`Swiper`)的宽度|`document.documentElement.clientWidth`|否|
|clientH|Number|滑动容器框(`Swiper`)的宽度|200|否|
|showCounter|Boolean|是否需要默认的计数器|false|否|
|counterStyle|Object|自定义默认计数器的样式 <br>只有当 `showCounter`为 `true`时才有效|null|否|
|startIndex|Number|起始渲染显示的SwiperItem index|0|否|
|criticalValue|Number|临界点的比例值，当超过此值代表的临界点，则将自动滑动到下一张图片|1/3|否|
|autoPlayDelay|Number|如果指定了此参数，并且值 `>= 0`，则将会将此值当做 自动轮播`delay`的时间(单位为 `ms`)进行自动轮播；不指定则不自动轮播 <br>如果想要指定此值，一般建议设置为 `3000`|null|否|
|duration|Number|自动滚动到稳定位置所需的时间，单位是秒(ms)|350|否|
|noDragWhenSingle|Boolean|如果只有一个 swipeItem，是否禁止拖动|true|否|

### Events

|事件名|描述|参数|
|---|---|---|
|click|整个组件的点击事件（可用于控制组件的显示/隐藏）|activeIndex|
|change|每次滚动结束后的回调|activeIndex|

## 额外的能力

### goto

`Swiper`组件提供了一个 `goto`方法，允许使用者自行指定滑动到哪一个 `SwiperItem`的位置：

```js
this.$refs.mySwiper.goto(2)
```
`goto`方法接收一个 `number`类型的参数(`index`)，表示将要滑动到的 `SwiperItem`的位置
参数 `index`从 `0` 开始

具体参见示例 [example - basic-slot](https://github.com/accforgit/vue-active-swiper/blob/master/example/basic-slot.vue)

### 具名 slot

`Swiper`组件还可以接收一个 `name`为 `extra`的具名 `slot`，方便开发者更加自由地定制化组件:

```html
<Swiper>
  <SwiperItem>1</SwiperItem>
  <SwiperItem>2</SwiperItem>
  <SwiperItem>3</SwiperItem>
  <!-- 具名 slot -->
  <p slot="extra">custom extra content</p>
</Swiper>
```

## License

MIT
