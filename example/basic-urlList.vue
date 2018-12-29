<template>
  <Swiper :urlList="urlList" :clientH="200" :showCounter="true" :autoPlayDelay="1500" :criticalValue="1/4" />
</template>

<script>
import 'vue-active-swiper/dist/VueActiveSwiper.css'
import { Swiper } from 'vue-active-swiper'

export default {
  components: {
    Swiper
  },
  data () {
    return {
      urlList: [
        'https://dummyimage.com/375x100/FB8A80?text=0',
        'https://dummyimage.com/375x100/29A90F?text=1',
        'https://dummyimage.com/375x100/6F9DFF?text=2'
      ]
    }
  }
}
</script>

