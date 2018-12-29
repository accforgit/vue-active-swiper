<template>
  <div class="swiper-item">
    <slot></slot>
  </div>
</template>

<script>
export default {
  name: 'SwiperItem'
}
</script>

<style scoped>
.swiper-item {
  display: inline-block;
  width: 100%;
  height: 100%;
  overflow: hidden;
}
</style>
