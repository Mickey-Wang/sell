<template>
  <div class="cartcontrol">

      <div class="cart-decrease" @click="decreaseCart">
        <transition name="move">
          <span class="inner icon-remove_circle_outline" v-show="food.count>0"></span>
        </transition>
      </div>

    <div class="cart-count" v-show="food.count>0">{{food.count}}</div>
    <div class="cart-add icon-add_circle" @click="addCart"></div>
  </div>
</template>

<script type="text/ecmascript-6">
  import Vue from 'vue'

  export default {
    props: {
      food: {
        type: Object
      }
    },
    methods: {
      addCart(event) {
        if (!event._constructed) {
          return
        }
        if (!this.food.count) {
//          this.food.count = 1
          Vue.set(this.food, 'count', 1) // 设置对象的属性。如果对象是响应式的，确保属性被创建后也是响应式的，同时触发视图更新。这个方法主要用于避开 Vue 不能检测属性被添加的限制。
        } else {
          this.food.count++
        }
      },
      decreaseCart(event) {
        if (!event._constructed) {
          return
        }
        if (this.food.count) {
          this.food.count--
        }
      }
    }
  }
</script>

<style lang="stylus" rel="stylesheet/stylus">
  .cartcontrol
    font-size: 0
    .cart-decrease
      display: inline-block
      padding: 6px// 为了使得点击区域更大，而且不影响样式展示，所以加一个padding
      .inner
        display: inline-block
        line-height: 24px
        font-size: 24px
        color: rgb(0, 160, 220)
    .move-enter-active, .move-leave-active
      transition: transform 1s linear
    .move-enter-to, .move-leave
      transform: translate3d(0px, 0, 0) scale(1) rotate(0deg) // 注意书写顺序
    .move-enter, .move-leave-to
      transform: translate3d(36px, 0, 0) scale(0) rotate(180deg)
    .cart-count
      display: inline-block
      vertical-align: top
      width: 12px
      padding-top: 6px
      line-height: 24px
      text-align: center
      font-size: 10px
      color: rgb(147, 153, 159)
    .cart-add
      display: inline-block
      padding: 6px
      line-height: 24px
      font-size: 24px
      color: rgb(0, 160, 220)
</style>
