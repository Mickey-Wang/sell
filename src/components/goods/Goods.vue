<template>
  <div class="goods">
    <div class="menu-wrapper" ref="menuWrapper">
      <ul>
        <li v-for="(item, index) in goods" class="menu-item" :class="{'current':currentIndex===index}"
            @click="_selectMenu(index, $event)">
          <span class="text">
            <span v-show="item.type>=0" class="icon" :class="classMap[item.type]"></span>{{item.name}}
          </span>
        </li>
      </ul>
    </div>
    <div class="foods-wrapper" ref="foodsWrapper">
      <ul>
        <li v-for="item in goods" class="food-list food-list-hook"><!-- 这里加hook后缀代表该class只是作为选择器钩子，无样式绑定 -->
          <h1 class="title">{{item.name}}</h1>
          <ul>
            <li v-for="food in item.foods" class="food-item">
              <div class="icon">
                <img width="57px" height="57px" :src="food.icon">
              </div>
              <div class="content">
                <h2 class="name">{{food.name}}</h2>
                <p class="desc">{{food.description}}</p>
                <div class="extra">
                  <span class="count">月售{{food.sellCount}}份</span><span>好评率{{food.rating}}</span>
                </div>
                <div class="price">
                  <span class="now">￥{{food.price}}</span><span class="old"
                                                                v-show="food.oldPrice">￥{{food.oldPrice}}</span>
                </div>
              </div>
            </li>
          </ul>
        </li>
      </ul>
    </div>
  </div>
</template>

<script type="text/ecmascript-6">

  import BScroll from 'better-scroll'

  const ERR_OK = 0

  export default {
    data() {
      return {
        goods: [],
        listHeight: [],
        scrollY: 0
      }
    },
    props: {
      seller: {
        type: Object
      }
    },
    computed: {
      /**
       * 计算当前滚动落在左侧菜单数组中的哪个索引
       */
      currentIndex() {
        for (let i = 0; i < this.listHeight.length; i++) {
          let height1 = this.listHeight[i]
          let height2 = this.listHeight[i + 1]
          if (!height2 || (this.scrollY >= height1 && this.scrollY < height2)) {
            return i
          }
        }
      }
    },
    methods: {
      _selectMenu(index, event) {
        if (!event._constructed) return // 如果事件是自定义派发的事件，那么event.constructed是true。这里如果是原生点击事件那么直接返回，为避免BS插件在PC中点击列表时触发两次注册的点击事件。
        let foodList = this.$refs.foodsWrapper.getElementsByClassName('food-list-hook')
        let el = foodList[index]
        this.foodsScroll.scrollToElement(el, 300) // 动画执行时间300毫秒
      },
      _initScroll() {
        // https://github.com/ustbhuangyi/better-scroll/blob/master/README.md
        this.menuScroll = new BScroll(this.$refs.menuWrapper, {
          click: true // 在移动设备中，betterScroll会把点击preventDefault（在PC中点击列表时可以触发默认点击行为，而在手机中阻止点击默认行为，所以这里相当于显示的声明告知BS，需要派发一个自定义点击事件）
        })
        this.foodsScroll = new BScroll(this.$refs.foodsWrapper, {
          probeType: 3
        })
        this.foodsScroll.on('scroll', (pos) => {
          this.scrollY = Math.abs(Math.round(pos.y))
        })
      },
      /**
       * 计算右侧每个li的起止高度
       */
      _calculateHeight() {
        let foodList = this.$refs.foodsWrapper.getElementsByClassName('food-list-hook')
        let height = 0
        /* getElementsByName()和getElementsByTagName()都返回NodeList对象，而类似document.images和document.forms的属性为HTMLCollection对象
         * http://www.cnblogs.com/zczhangcui/p/6305680.html
         */
        this.listHeight = Array.prototype.map.call(foodList, (item) => {
          height += item.clientHeight
          return height
        })
        this.listHeight.unshift(0)
      }
    },
    created() {
      this.classMap = ['decrease', 'discount', 'special', 'invoice', 'guarantee']
      this.$http.get('/api/goods').then((response) => {
        response = response.body
        if (response.errno === ERR_OK) {
          this.goods = response.data
          // 为了在数据变化之后等待 Vue 完成更新 DOM  https://cn.vuejs.org/v2/guide/reactivity.html#异步更新队列
          this.$nextTick(() => {
            this._initScroll()
            this._calculateHeight()
          })
        }
      })
    }
  }
</script>

<style lang="stylus" rel="stylesheet/stylus">
  @import "../../common/stylus/mixin.styl"

  .goods
    display: flex
    position: absolute //因为上中下布局，上下高度固定，中间高度需要自适应，所以这里用absolute
    top: 174px
    bottom: 46px
    width: 100%
    overflow: hidden
    .menu-wrapper
      flex: 0 0 80px
      background: #f3f5f7
      .menu-item
        display: table // http://www.zhangxinxu.com/wordpress/2010/10/%E6%88%91%E6%89%80%E7%9F%A5%E9%81%93%E7%9A%84%E5%87%A0%E7%A7%8Ddisplaytable-cell%E7%9A%84%E5%BA%94%E7%94%A8/
        height: 54px
        width: 56px
        padding: 0 12px
        line-height: 14px
        &.current
          position: relative
          z-index: 10
          margin-top: -1px
          background: #fff
          font-weight: 700
          .text
            border-none()
        .icon
          display: inline-block
          width: 14px
          height: 14px
          vertical-align: top
          margin-right: 2px
          background-size: 14px 14px
          background-repeat: no-repeat
          &.decrease
            bg-image('decrease_3')
          &.discount
            bg-image('discount_3')
          &.guarantee
            bg-image('guarantee_3')
          &.invoice
            bg-image('invoice_3')
          &.special
            bg-image('special_3')
        .text
          display: table-cell // 让大小不固定元素垂直居中
          width: 56px
          vertical-align: middle
          border-1px(rgba(7, 17, 27, 0.1))
          font-size: 12px
          font-weight: 200
    .foods-wrapper
      flex: auto
      .title
        height: 26px
        line-height: 26px
        border-left: 2px solid #d9dde1
        background: #f3f5f7
        padding-left: 14px
        font-size: 12px
        color: rgb(147, 153, 159)
      .food-item
        display: flex
        margin: 18px 18px 0
        padding-bottom: 18px // 为了使得一像素边框与内容相距18px，margin不算内容
        border-1px(rgba(7, 17, 27, 0.1))
        &:last-child
          border-none()
        .icon
          flex: 0 0 57px
          margin-right: 10px
        .content
          flex: auto
          .name
            margin: 2px 0 8px
            height: 14px
            line-height: 14px
            font-size: 14px
            color: rgb(7, 17, 27)
          .desc, .extra
            line-height: 10px
            font-size: 10px
            color: rgb(147, 153, 159)
          .desc
            line-height: 12px
            margin-bottom: 8px
          .extra
            .count
              margin-right: 12px
          .price
            font-weight: 700
            line-height：24px
            .now
              margin-right: 8px
              font-size: 14px
              color: rgb(240, 20, 20)
            .old
              text-decoration: line-through
              font-size: 10px
              color: rgb(147, 153, 159)
</style>
