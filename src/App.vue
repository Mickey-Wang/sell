<template>
  <div id="app">
    <v-header></v-header>
    <div class="tab border-1px">
      <div class="tab-item">
        <router-link to="/goods">商品</router-link>
      </div>
      <div class="tab-item">
        <router-link to="/ratings">评论</router-link>
      </div>
      <div class="tab-item">
        <router-link to="/seller">商家</router-link>
      </div>
    </div>
    <router-view></router-view>
  </div>
</template>

<script>
  import Header from '@/components/header/Header.vue'

  export default {
    components: {
      'v-header': Header
    }
  }
</script>

<style lang="stylus" rel="stylesheet/stylus">
  @import "common/stylus/mixin.styl" // TODO mixin是要必须手动导入吗？不能在index中引入？

  //css书写规范：先写像display，float，position这样影响布局位置的，再写像width、height这样不可继承的，最后是像font-size、color这样可继承的
  #app
    .tab
      display: flex //vue-loader会依赖一个叫postcss的插件，这会加入css兼容写法的问题
      width: 100%
      height: 40px
      line-height: 40px //设计稿中标注的是iPhone6的物理像素，80px，因为iPhone6的dpr是window.devicePixelRatio=2，所以此处写独立像素值40px
      //border-bottom: 1px solid rgba(7, 17, 27, 0.1)
      boder-1px(rgba(7, 17, 27, 0.1)) // 使用mixin中的函数
      .tab-item
        flex: auto
        text-align: center
        & > a //&代表.tab-item的这个父引用
          display: block // 为使得点击a标签充满整个父容器，方便点击
          font-size: 14px
          color: rgb(77, 85, 93)
          &.active
            color: rgb(240, 20, 20)
</style>
