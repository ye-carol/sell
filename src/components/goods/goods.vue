<template>
  <div class="">
    <div class="goods">
      <div class="menu-wrapper" ref="menuWrapper">
        <ul class="">
          <li v-for="(item,index) in goods" class="menu-item" :class="{'current': index === currentIndex}"  @click="selectMenu(index,$event)" ref="menuList">
          <span class="text">
            <span v-show="item.type > 0" class="icon" :class="classMap[item.type]"></span>{{item.name}}
          </span>
          </li>
        </ul>
      </div>
      <div class="foods-wrapper" ref="foodsWrapper">
        <ul>
          <li v-for="(item,index) in goods" class="food-list food-list-hook" ref="foodList">
            <h1 class="title" :class="{'current': index === currentIndex}">{{item.name}}</h1>
            <ul>
              <li v-for="food in item.foods" class="food-item border-1px" @click="selectFood(food,$event)">
                <div class="icon">
                  <img width="57" :src="food.icon" alt="">
                </div>
                <div class="content">
                  <div class="name">{{food.name}}</div>
                  <div class="desc">{{food.description}}</div>
                  <div class="extra">
                    <span class="count">月售{{food.sellCount}}</span><span>好评率{{food.rating}}</span>
                  </div>
                  <div class="price">
                    <span class="now">￥{{food.price}}</span><span class="old" v-show="food.oldPrice">￥{{food.oldPrice}}</span>
                  </div>
                  <div class="cartControl-wrapper">
                    <cartControl @add="addFood" :food="food"></cartControl>
                  </div>
                </div>
              </li>
            </ul>
          </li>
        </ul>
      </div>
      <shopCart ref="shopCart" :select-foods="selectFoods" :delivery-price="seller.deliveryPrice" :min-price="seller.minPrice"></shopCart>
    </div>
    <food ref="food"  :food="selectedFood"></food>
  </div>

</template>

<script type="text/ecmascript-6">
  import BScroll from 'better-scroll'
  import shopCart from 'components/shopCart/shopCart'
  import cartControl from 'components/cartControl/cartControl'
  import food from 'components/food/food'
  const ERR_OK = 0
  export default {
    name: 'goods',
    props: {
      seller: {
        type: Object
      }
    },
    data () {
      return {
        goods: [],
        listHeight: [],
        scrollY: 0,
        selectedFood: {}
      }

    },
    computed: {
      currentIndex () {
        for(var i = 0; i < this.listHeight.length; i++) {
          let height1 = this.listHeight[i]
          let height2 = this.listHeight[i + 1]
          if (!height2 || this.scrollY > height1 && this.scrollY < height2) {
            return i
          }else{
            return 0
          }
        }
      },
      selectFoods () {
        let foods = []
        this.goods.forEach((good) => {
          good.foods.forEach((food) => {
            if (food.count) {
              foods.push(food)
            }
          })
        })
        return foods
      }
    },
    mounted () {
      this.$http.get('/api/goods').then((response) => {
        response = response.body
        if (response.errno === ERR_OK) {
          this.goods = response.data
        }
      })
      this.classMap = ['decrease', 'discount', 'special', 'invoice', 'guarantee']
      this.$nextTick(function () {
        this._initScroll()
        this._calculateHeight()
      })
    },
    methods: {
      selectMenu(index, event) {
        if (!event._constructed) {
          return;
        }
        let foodList = this.$refs.foodList;
        let el = foodList[index];
        this.foodsScroll.scrollToElement(el, 300);
      },
      _initScroll () {
        this.menuScroll = new BScroll(this.$refs.menuWrapper,{
          click: true
        })
        this.foodsScroll = new BScroll(this.$refs.foodsWrapper,{
          click: true,
          probeType: 3
        })
        this.foodsScroll.on('scroll', (pos) => {
          this.scrollY = Math.abs(Math.round(pos.y))
        })
      },
      _calculateHeight () {
        let foodList = this.$refs.foodsWrapper.getElementsByClassName('food-list-hook');
        let height = 0;
        this.listHeight.push(height);
        for (let i = 0; i < foodList.length; i++) {
          let item = foodList[i];
          height += item.clientHeight;
          this.listHeight.push(height);
        }
      },
      _followScroll (index) {
        let menuList = this.$refs.menuList
        let el = menuList[index]
        this.menuScroll.scrollToElement(el,300,-100)
      },
      addFood (target) {
        this._drop(target)
      },
      _drop (target) {
        //体验优化，异步执行下落动画
        this.$nextTick(() => {
          this.$refs.shopCart.drop(target)
        })
      },
      selectFood (food,event) {
        if (!event._constructed) {
          return;
        }
        this.selectedFood = food
        this.$refs.food.show()
      }
    },
    components: {
      shopCart: shopCart,
      cartControl: cartControl,
      food: food
    }
  }
</script>

<style lang="stylus" rel="stylesheet/stylus">
  @import "../../common/stylus/mixin.styl"
  .goods
    display: flex
    position: absolute
    top: 174px
    bottom: 4px
    left: 0
    overflow hidden
    .menu-wrapper
      flex: 0 0 80px
      width: 80px
      background #f3f5f7
      .menu-item
        display: table
        padding: 0 12px
        width: 56px
        height: 54px
        line-height: 14px
        &.current
          position: relative
          margin-top: -1px
          z-index: 10px
          background: #fff
          font-weight: 700
          .text
            border-none()
        .icon
         display: inline-block
         vertical-align: top
         font-size: 12px
         margin-right: 2px
         width: 12px
         height 12px
         background-size: 12px 12px
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
          display: table-cell
          width: 56px
          vertical-align: middle
          font-size: 12px
          border-1px(rgba(7,17,27,0.1))
    .foods-wrapper
      flex: 1
      .title
        padding-left: 14px
        height: 26px
        line-height: 26px
        font-size: 12px
        color: rgb(147,153,159)
        background: #f3f5f7
        border-left: 2px solid #d9dde1
      .food-item
        display: flex
        margin: 18px
        padding-bottom: 18px
        border-1px: rgba(7,17,27,0.1)
        &:last-child
          border-none()
          margin-bottom: 0
        .icon
          flex: 0 0 57px
          margin-right: 10px
          vertical-align: top
        .content
          flex: 1
          .name
            margin-top 2px
            font-size 14px
            color: rgb(7,17,27)
            line-height: 14px
          .desc
            line-height: 12px
          .desc,.extra
            margin-top: 8px
            font-size: 10px
            color: rgb(147,153,159)
          .extra .count{
            margin-right: 12px
          }
          .price
            margin-top: 8px
            line-height: 24px
            .now
              margin-right 8px
              font-size: 14px
              color: rgb(240,20,20)
              font-weight: 700
            .old
              text-decoration line-through
              font-size: 10px
              color: rgb(147,153,159)
              font-weight: 700
          .cartControl-wrapper
            position: absolute
            right: 0
            bottom: 0
</style>
