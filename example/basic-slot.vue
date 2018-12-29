<template>
  <div>
    <Swiper ref="mySwiper">
      <SwiperItem>
        <div class="item-content item1">SwiperItem - 0</div>
      </SwiperItem>
      <SwiperItem>
        <div class="item-content item2">SwiperItem - 1</div>
      </SwiperItem>
      <SwiperItem>
        <div class="item-content item3">SwiperItem - 2</div>
      </SwiperItem>
    </Swiper>
    <input type="number" v-model="gotoIndex" />
    <button @click="goto">goto</button>
  </div>
</template>

<script>
import 'vue-active-swiper/dist/VueActiveSwiper.css'
import { Swiper, SwiperItem } from 'vue-active-swiper'

export default {
  components: {
    Swiper,
    SwiperItem
  },
  data () {
    return {
      gotoIndex: 0
    }
  },
  methods: {
    goto () {
      this.$refs.mySwiper.goto(this.gotoIndex)
    }
  }
}
</script>

<style>
.item-content {
  display: flex;
  justify-content: center;
  align-items: center;
  height: 100%;
  background-color: #00FFCC;
}
.item2 {
  background-color: #669966;
}

.item3 {
  background: #9999CC;
}
</style>
