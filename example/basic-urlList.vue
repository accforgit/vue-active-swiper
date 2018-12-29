<template>
  <Swiper :urlList="urlList" :clientH="200" :showCounter="true" :autoPlayDelay="1500" :criticalValue="1/4" />
</template>

<script>
import 'vue-active-swiper/dist/VueActiveSwiper.css'
import { Swiper } from 'vue-active-swiper'

export default {
  components: {
    Swiper
  }
}
</script>

