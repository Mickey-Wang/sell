<template>
  <div class="header">
    <div class="content-wrapper">
      <div class="avatar">
        <img :src="seller.avatar" width="64" height="64">
      </div>
      <div class="content">
        <div class="title">
          <span class="brand"></span>
          <span class="name">{{ seller.name }}</span>
        </div>
        <div class="description">
          {{seller.description}}/{{seller.deliveryTime}}分钟送达
        </div>
        <!--这里加if判断是避免ajax数据响应之前渲染数据时报错-->
        <div class="support" v-if="seller.supports">
          <span class="icon" :class="classMap[seller.supports[0].type]"></span>
          <span class="text">{{seller.supports[0].description}}</span>
        </div>
      </div>
      <div class="support-count" v-if="seller.supports" @click="showDetail">
        <span class="count">{{seller.supports.length}}个</span>
        <i class="icon-keyboard_arrow_right"></i>
      </div>
    </div>
    <div class="bulletin-wrapper" @click="showDetail">
      <span class="bulletin-title"></span><span class="bulletin-text">{{seller.bulletin}}</span>
      <i class="icon-keyboard_arrow_right"></i>
    </div>
    <div class="background">
      <img :src="seller.avatar" width="100%" height="100%">
    </div>
    <div v-show="detailShow" class="detail">
      <div class="detail-wrapper clearfix"><!-- 这里的clearfix是清除子元素中margin-top所带来的影响，将第一个子元素的margin-top也算作整个父元素中高度的一部分 -->
        <div class="detail-main">
          <h1 class="name">{{seller.name}}</h1>
          <div class="star-wrapper">
            <star :size="48" :score="seller.score"></star>
          </div>
          <div class="title">
            <div class="line"></div>
            <div class="text">优惠信息</div>
            <div class="line"></div>
          </div>
        </div>
      </div>
      <div class="detail-close">
        <i class="icon-close"></i>
      </div>
    </div>
  </div>
</template>

<script type="text/ecmascript-6">
  import Star from '@/components/star/Star'

  export default {
    data() {
      return {
        detailShow: false
      }
    },
    methods: {
      showDetail() {
        this.detailShow = true
      }
    },
    props: {
      seller: {
        type: Object
      }
    },
    components: {
      Star
    },
    created() {
      this.classMap = ['decrease', 'discount', 'special', 'invoice', 'guarantee']
    }
  }
</script>

<style lang="stylus" rel="stylesheet/stylus">
  @import "../../common/stylus/mixin.styl"

  .header
    position: relative
    color: #fff
    background: rgba(7, 17, 27, 0.5)
    overflow: hidden // 去掉filter模糊后的下部外溢阴影
    .content-wrapper
      position: relative
      padding: 24px 12px 18px 24px
      font-size: 0//为了100%还原设计稿，使得头像与右侧内容间隔32px，所以需要去掉之间的空白字符，然后再添加margin-left;在后边每个 最末级 文字容器中添加font-size覆盖此属性
      .avatar
        display: inline-block
        img
          border-radius: 2px
      .content
        vertical-align: top // http://www.w3school.com.cn/cssref/pr_pos_vertical-align.asp
        display: inline-block
        margin-left: 16px
        .title
          margin: 2px 0 8px
          .brand
            display: inline-block
            vertical-align: top
            width: 30px
            height: 18px
            bg-image('brand')
            background-size: 30px 18px
          .name
            margin-left: 6px
            font-size: 16px
            line-height: 18px
            font-weight: bold
        .description
          margin-bottom: 12px
          line-height: 12px
          font-size: 12px
        .support
          .icon
            display: inline-block
            width: 12px
            height: 12px
            margin-right: 4px
            &.decrease
              bg-image('decrease_1')
            &.discount
              bg-image('discount_1')
            &.guarantee
              bg-image('guarantee_1')
            &.invoice
              bg-image('invoice_1')
            &.special
              bg-image('special_1')
            background-size: 12px 12px
            background-repeat: no-repeat
          .text
            vertical-align: top
            line-height: 12px
            font-size: 10px

      .support-count
        position: absolute
        right: 12px
        bottom: 14px
        padding: 0 8px
        height: 24px
        line-height: 24px
        border-radius: 14px
        background: rgba(0, 0, 0, 0.2)
        text-align: center
        .count
          font-size: 10px
          vertical-align: middle
        .icon-keyboard_arrow_right
          vertical-align: middle
          margin-left: 2px
          font-size: 10px
    .bulletin-wrapper
      position: relative
      height: 28px
      line-height: 28px
      padding: 0 28px 0 12px
      white-space: nowrap
      overflow: hidden
      text-overflow: ellipsis
      background: rgba(7, 17, 27, 0.2)
      /*font-size: 0px 不能用这种去掉空白字符，会影响到三个点的显示，所以删掉dom中缩进的空白字符*/
      .bulletin-title
        display: inline-block
        width: 22px
        height: 12px
        bg-image('bulletin')
        background-size: 22px 12px
        background-repeat: no-repeat
        vertical-align: top
        margin-top: 8px // (28-12)/2 让title和text都顶端对齐，然后控制其中一个居中
      .bulletin-text
        margin-left: 4px
        vertical-align: top
        font-size: 10px
      .icon-keyboard_arrow_right
        position: absolute
        font-size: 10px
        right: 12px
        top: 8px
    .background
      position: absolute
      top: 0
      left: 0
      width: 100%
      height: 100%
      z-index: -1
      filter: blur(10px)
    .detail
      position: fixed
      top: 0
      left: 0
      z-index: 100
      width: 100%
      height: 100%
      overflow: auto
      background: rgba(7, 17, 27, 0.8)
      .detail-wrapper//  Sticky footers start; http://www.w3cplus.com/css3/css-secrets/sticky-footers.html关注flex
        min-height: 100%
        width: 100%
        .detail-main
          margin-top: 64px
          padding-bottom: 64px // 为关闭X预留
        .name
          line-height: 16px
          font-size: 16px
          font-weight: 700
          text-align: center
        .star-wrapper
          margin-top: 18px
          padding: 2px 0// 设计稿中高度为48，而星星的高度为40，所以加8px的padding
          text-align: center
        .title
          display: flex// postCss loader扩展CSS hack写法，与caniuse.com同步
          width: 80%
          margin: 30px auto 24px
          .line
            flex: 1
            position: relative
            top: -6px
            border-bottom: 1px solid rgba(255,255,255,0.2)
          .text
            padding: 0 12px
            font-size: 14px
      .detail-close
        position: relative
        width: 32px
        height: 32px
        margin: -64px auto 0
        font-size: 32px// Sticky footers end;
</style>
