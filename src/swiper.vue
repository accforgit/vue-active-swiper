<template>
  <div
    class="active-swiper-container"
    ref="swiperContainer"
    :style="{ width: clientW + 'px', height: clientH + 'px' }"
    @click="swipeClick">
    <div class="swiper-wrapper" ref="sliderWrapper" :style="{
        transform: `translate3d(${transX}px, 0, 0)`,
        transition: isTransToX ? `transform ${duration}ms cubic-bezier(0, 0, 0.25, 1)` : ''
      }"
      @touchstart="touchstartFn"
      @touchmove="touchmoveFn"
      @touchend="touchendFn"
      @transitionend="transitionendFn">
      <template v-if="urlList">
        <div class="img-box" v-for="item in currentList" :key="item._id" :style="{
          backgroundImage: `url(${item.url})`,
          backgroundSize
        }"></div>
      </template>
      <template v-else>
        <component :is="firstSwiperItem"></component>
        <slot></slot>
        <component :is="lastSwiperItem"></component>
      </template>
    </div>
    <!-- 计数器 -->
    <span v-if="showCounter && swiperItemCount > 1" class="swiper-pagination" :style="counterStyle">
      {{activeIndex}}/{{swiperItemCount - 2}}
    </span>
    <!-- 允许添加额外的自定义DOM -->
    <slot name="extra"></slot>
  </div>
</template>

<script>
// 滚动容器的宽度
let clientW = 375
let criticalWidth = 0
// 用户标识当前的滑动状态
let touchStatus = 0

// 单指操作 - 滑动坐标相关
// touchStart 点击坐标
let stStartX = 0
// 上个周期中的tranlateX 坐标
let stPrevX = 0
// 当前是否需要自动滑动到下一张图片
let stAutoNext = 0
// 滑动的方向，-1 为右滑，1 为左滑
let stDirectionFlag = 0

// 当前正在预览的图片次序，用于位置计算
let activeIndex = 0
// sliderWrapper上的触摸点数量
let touchCount = 0
// 用于取消自动轮播（如果指定了的话）
let autoPlayTimer = null
// getBoundingClientRect的兼容性
const isSupportGetBoundingClientRect = typeof document.documentElement.getBoundingClientRect === 'function'

export default {
  name: 'Swiper',
  props: {
    urlList: {
      type: Array,
      required: false,
      default: null
    },
    // 图片以何种缩放的形式铺在 滑动容器框内，只有当指定了 urlList 时才有效
    backgroundSize: {
      type: String,
      required: false,
      default: 'cover'
    },
    // swiperContainer容器的宽度
    clientW: {
      type: Number,
      required: false,
      default: document.documentElement.clientWidth
    },
    // swiperContainer容器的高度
    clientH: {
      type: Number,
      required: false,
      default: 200
    },
    // 是否需要默认的计数器
    showCounter: {
      type: Boolean,
      required: false,
      default: false
    },
    // 自定义默认计数器的样式 
    counterStyle: {
      type: Object,
      required: false,
      default: null
    },
    // 起始index
    startIndex: {
      type: Number,
      required: false,
      default: 0
    },
    // 临界点的比例值，当超过这个临界点，则需要自动滑动到下一张图片
    criticalValue: {
      type: Number,
      required: false,
      default: 1 / 3
    },
    // 如果指定了此参数，并且值 >= 0，则将会将此值当做 delay的时间(单位为 ms)进行自动轮播；
    // 不指定或指定值小于 0 则不自动轮播
    // 如果想要指定此值，一般建议设置为 3000
    autoPlayDelay: {
      type: Number,
      required: false,
      default: null
    },
    // 自动滚动到稳定位置所需的时间，单位是秒(ms)
    duration: {
      type: Number,
      required: false,
      default: 350,
      validator(value) {
        return value >= 0
      }
    },
    // 如果只有一个 swipeItem，是否允许拖动
    noDragWhenSingle: {
      type: Boolean,
      required: false,
      default: true
    }
  },
  data () {
    return {
      // 当前显示在DOM中的图片列表
      currentList: [],
      // 单指滑动的位移
      transX: 0,
      // 当前正在预览的图片次序，用于给模板渲染计数器使用
      activeIndex: 0,
      // 正在自动滚动到固定位置的过程中
      isTransToX: false,
      firstSwiperItem: null,
      lastSwiperItem: null,
      swiperItemCount: 0
    }
  },
  created () {
    if (this.urlList) {
      this.currentList = this.urlList.length > 1
        ? this.urlList.slice(-1).concat(this.urlList, this.urlList.slice(0, 1)).map((url, index) => ({ url, _id: index }))
        : this.urlList.map((url, index) => ({ url, _id: index }))
      this.swiperItemCount = this.currentList.length
    } else {
      const slots = this.$slots.default || []
      this.swiperItemCount = slots.length
      if (slots.length > 1) {
        // 加上首尾多出来的两个
        this.swiperItemCount = slots.length + 2
        this.updateChild(slots)
      }
    }
    clientW = this.clientW
    criticalWidth = clientW * this.criticalValue
    if (this.swiperItemCount > 1) {
      // 因为首尾都多加了一个swiperItem元素，所以顺延一位
      this.activeIndex = activeIndex = this.getActiveIndex(this.startIndex + 1)
      this.transX = stPrevX = -clientW * activeIndex
    }
    // 自动轮播
    setTimeout(() => {
      this.autoPlayFn()
    }, 14)
  },
  destroy () {
    clearTimeout(autoPlayTimer)
  },
  watch: {
    autoPlayDelay () {
      // 修改了 autoPlayDelay 的值，需要重新触发事件
      this.autoPlayFn()
    }
  },
  methods: {
    touchstartFn (e) {
      // 取消还没结束的自动轮播（如果指定了轮播的话）
      clearTimeout(autoPlayTimer)
      if (this.ignoreTouch()) return
      if (this.isTransToX) {
        if (!isSupportGetBoundingClientRect) {
          return touchStatus = 0
        }
        this.isTransToX = false
        this.transX = stPrevX = this.$refs.sliderWrapper.getBoundingClientRect().left - this.$refs.swiperContainer.getBoundingClientRect().left
      }
      touchStatus = 1
      touchCount = e.touches.length
      this.singleTouchStartFn(e)
    },
    touchmoveFn (e) {
      e.preventDefault()
      if (this.ignoreTouch() || touchStatus !== 1) return
      if (this.swiperItemCount !== 1) {
        this.singleTouchMoveFn(e)
      } else {
        // swiperItem 数量为1
        if (!this.noDragWhenSingle) {
          // 允许拖动
          this.transX = (e.touches[touchCount - 1].clientX - stStartX) * 0.1 + stPrevX
        }
      }
    },
    touchendFn (e) {
      touchCount = e.touches.length
      if (this.ignoreTouch() || touchStatus !== 1) return
      if (this.swiperItemCount !== 1 || !this.noDragWhenSingle) {
        if (touchCount !== 0) {
          stStartX = e.touches[touchCount - 1].clientX
          stPrevX = this.transX
          return
        }
        this.singleTouchEndFn(e)
      }
    },
    // 单指滑动行为 - start
    singleTouchStartFn (e) {
      stStartX = e.touches[touchCount - 1].clientX
      if (touchCount > 1) {
        stPrevX = this.transX
      }
    },
    // 单指滑动行为 - move
    singleTouchMoveFn (e) {
      let transX = e.touches[touchCount - 1].clientX - stStartX + stPrevX
      if (transX > 0) {
        // 滑动到到第一个了
        stStartX = e.touches[touchCount - 1].clientX
        // 矫正到正确位置
        stPrevX = transX = -clientW * (this.swiperItemCount - 2)
      } else if (transX < -clientW * (this.swiperItemCount - 1)) {
        // 滑动到最后一个了
        stStartX = e.touches[touchCount - 1].clientX
        // 矫正到正确位置
        stPrevX = transX = -clientW
      }
      this.transX = transX
    },
    // 单指滑动行为 - end
    singleTouchEndFn () {
      let toX = this.swiperItemCount === 1 ? 0 : this.getSingleTouchEndMultipleChildToX()
      this.gotoX(toX)
    },
    // 单指滑动行为 - end，swiper-item数量大于 1 的情况
    getSingleTouchEndMultipleChildToX () {
      let toX = 0
      let diffX = this.transX + clientW * activeIndex
      const wholeBlock = Math.floor(diffX / clientW)
      // 如果连续滑过超过一个 swiperItem 块，当做一个来处理
      if (Math.abs(diffX) > clientW) {
        activeIndex = Math.ceil(-this.transX / clientW)
        diffX = diffX - clientW * wholeBlock
      }
      // diffX 大于0 说明是右滑，小于0 则是左滑
      if (diffX > 0) {
        stDirectionFlag = -1
        stAutoNext = diffX > criticalWidth
        toX = stAutoNext ? -clientW * (activeIndex - 1) : -clientW * activeIndex
      } else if (diffX < 0) {
        stDirectionFlag = 1
        stAutoNext = Math.abs(diffX) > criticalWidth
        toX = stAutoNext ? -clientW * (activeIndex + 1) : -clientW * activeIndex
      } else {
        stDirectionFlag = 0
        stAutoNext = false
        toX = -clientW * activeIndex
      }
      this.activeIndex = activeIndex = this.getActiveIndex(activeIndex + (stAutoNext ? stDirectionFlag : 0))
      return toX
    },
    transEndFn () {
      this.activeIndex = activeIndex = this.getActiveIndex(activeIndex)
      this.transX = stPrevX = -clientW * activeIndex
      this.$emit('change', activeIndex)
      // setTimeout是为了避免当 autoPlayDelay值被指定为 0 时无限轮播出现问题
      // 16.7 是 1000/60 的大约值
      setTimeout(() => {
        this.autoPlayFn()
      }, 16.7)
    },
    transitionendFn (e) {
      if (e.target.className === 'swiper-wrapper') {
        if (this.isTransToX) {
          this.isTransToX = false
          // 单指操作 - 自动滑动结束
          this.transEndFn()
        }
      }
    },
    // 首尾多添加两个swiperItem，也就是swiperItem的元素数量比传进来的多两个
    updateChild (slots) {
      this.firstSwiperItem = {
        render (h) {
          return h('div', {
            staticClass: 'swiper-item-box'
          }, slots.slice(-1))
        }
      }
      this.lastSwiperItem = {
        render (h) {
          return h('div', {
            staticClass: 'swiper-item-box'
          }, slots.slice(0, 1))
        }
      }
    },
    getActiveIndex (index) {
      if (this.swiperItemCount === 1) return 0
      if (index >= this.swiperItemCount - 1) return 1
      if (index <= 0) return this.swiperItemCount - 2
      return index
    },
    autoPlayFn () {
      // 判断是否满足自动轮播的条件
      if (this.swiperItemCount > 1 && (typeof this.autoPlayDelay === 'number' && this.autoPlayDelay >= 0) && touchCount === 0 && this.transX % clientW === 0) {
        clearTimeout(autoPlayTimer)
        autoPlayTimer = setTimeout(() => {
          activeIndex = activeIndex + 1
          this.transX = -clientW * activeIndex
          this.isTransToX = true
          this.correctDurationAct()
        }, this.autoPlayDelay)
      }
    },
    // 如果没有传入 swiper-item子元素，或者只传入了一个子元素并且 noDragWhenSingle为 true，
    // 则不对 touch 事件进行滑动响应
    ignoreTouch () {
      return this.swiperItemCount === 0 || (this.swiperItemCount === 1 && this.noDragWhenSingle)
    },
    // duration不正确或为0，导致不会触发transitionend函数，所以需要直接调用 transEndFn
    correctDurationAct () {
      if (typeof this.duration !== 'number' || this.duration <= 0) {
        this.isTransToX = false
        this.transEndFn()
      }
    },
    // 组件的点击事件
    swipeClick () {
      this.$emit('click', this.activeIndex - 1)
    },
    gotoX (toX) {
      if (this.transX === toX) {
        // 已经手动滑到正确的位置
        this.transEndFn()
      } else {
        // 自由滚动到合适的位置
        this.isTransToX = true
        this.transX = toX
        this.correctDurationAct()
      }
    },
    goto (index) {
      clearTimeout(autoPlayTimer)
      activeIndex = index % (this.swiperItemCount - 2) + 1
      if (this.activeIndex !== activeIndex) {
        this.activeIndex = activeIndex
        this.gotoX(-clientW * activeIndex)
      } else {
        this.autoPlayFn()
      }
    }
  }
}
</script>

<style>
.active-swiper-container {
  position: relative;
  overflow: hidden;
  background-color: #eee;
}
.active-swiper-container * {
  box-sizing: border-box;
}
.active-swiper-container .swiper-wrapper {
  height: 100%;
  white-space: nowrap;
}
.active-swiper-container .swiper-item-box,
.active-swiper-container .img-box {
  display: inline-block;
  width: 100%;
  height: 100%;
  overflow: hidden;
}
.active-swiper-container .img-box {
  background-position: center center;
  background-repeat: no-repeat;
}
.active-swiper-container .swiper-pagination {
  position: absolute;
  bottom: 10px;
  right: 10px;
  height: 20px;
  line-height: 20px;
  padding: 0 10px;
  font-size: 12px;
  color: #fff;
  border-radius: 10px;
  background-color: rgba(100, 100, 100, .6);
}
</style>
