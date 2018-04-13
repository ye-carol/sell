<template>
  <transition name="move">
    <div class="food" v-show="showFlag" ref="food">
        <div class="food-content">
          <div class="image-header">
            <img :src="food.image">
            <div class="back" @click="hide">
              <i class="icon-arrow_lift"></i>
            </div>
          </div>
          <div class="content">
            <div class="name">{{food.name}}</div>
            <div class="detail">
              <span class="sell-count">月售{{food.sellCount}}</span>
              <span class="rating">好评率{{food.rating}}</span>
            </div>
            <div class="price">
              <span class="now">￥{{food.price}}</span><span class="old-price" v-show="food.oldPrice">￥{{food.oldPrice}}</span>
            </div>
            <div class="cartControl-wrapper">
              <cartControl @add="addFood" :food="food"></cartControl>
            </div>
            <transition name="fade"></transition>
            <div class="buy" v-show="!food.count || food.count === 0" @click="addFirst($event)">
              加入购物车
            </div>
          </div>
          <split  v-show="food.info"></split>
          <div class="info" v-show="food.info">
            <div class="title">商品信息</div>
            <p class="text">{{food.info}}</p>
          </div>
          <split v-show="food.ratings"></split>
          <div class="rating" v-show="food.ratings">
            <div class="title">商家评价</div>
            <ratingSelect @select="selectRating" @toggle="toggle" :ratings="food.ratings" :select-type = selectType :only-content="onlyContent" :desc="desc"></ratingSelect>
            <div class="rating-wrapper">
              <ul v-show="food.ratings && food.ratings.length">
                <li v-show="needShow(rating.rateType,rating.text)" v-for="rating in food.ratings"  class="rating-item">
                  <div class="user">
                    <span class="name">{{rating.username}}</span>
                    <img :src="rating.avatar" class="avatar" width="12" height="12">
                  </div>
                  <div class="time">{{rating.rateTime | formatDate}}</div>
                  <p class="text">
                    <span :class="{'icon-thumb_up':rating.rateType === 0,'icon-thumb_down':rating.rateType === 1}"></span>{{rating.text}}
                  </p>
                </li>
              </ul>
              <div class="no-rating" v-show="!food.ratings || food.ratings.length">暂无评价</div>
            </div>
          </div>
        </div>
    </div>
  </transition>
</template>

<script type="text/ecmascript-6">
  import Vue from 'Vue'
  import {formatDate} from 'common/js/date';
  import BScroll from 'better-scroll'
  import cartControl from 'components/cartControl/cartControl'
  import split from 'components/split/split'
  import ratingSelect from 'components/ratingSelect/ratingSelect'

  const POSITIVE = 0
  const NEGATIVE = 1
  const ALL = 2

  export default {
    name: 'food',
    props: {
      food: {
        type: Object
      }
    },
    data () {
      return {
        showFlag: false,
        selectType: ALL,
        onlyContent: true,
        desc: {
          all: '全部',
          positive: '推荐',
          negative: '吐槽'
        }
      }
    },
    methods: {
      show () {
        this.showFlag = true
        this.selectType = ALL
        this.onlyContent = false
        this.$nextTick(() => {
          if(!this.scroll) {
            this.scroll = new BScroll(this.$refs.food, {
              click: true
            })
          }else{
            this.scroll.refresh()
          }

        })
      },
      hide () {
        this.showFlag = false
      },
      addFirst(event) {
        if(!event._constructed) {
          return;
        }
        Vue.set(this.food,'count',1)
      },
      addFood (target) {
        this.$emit('add', target);
      },
      selectRating (type) {
        this.selectType = type
        this.$nextTick(() => {
          this.scroll.refresh()
        })
      },
      toggle () {
        this.onlyContent = !this.onlyContent
        this.$nextTick(() => {
          this.scroll.refresh()
        })
      },
      needShow (type,text) {
        if(this.onlyContent && !text) {
          return false
        }
        if(this.selectType === ALL) {
          return true
        }else{
          return type === this.selectType
        }
      }

    },
    filters: {
      formatDate(time) {
        let date = new Date(time);
        return formatDate(date, 'yyyy-MM-dd hh:mm');
      }
    },
    components: {
      cartControl: cartControl,
      split: split,
      ratingSelect: ratingSelect
    }
  }
</script>

<style lang="stylus" rel="stylesheet/stylus">
  .food
    position: fixed
    top: 0
    left: 0
    bottom: 48px
    width:100%
    z-index: 30
    background: #ffffff
    &.move-enter-active,&.move-leave-active
      transition: all 0.3s linear
      transform: translate3d(0,0,0)
    &.move-enter,&.move-leave
      transform: translate3d(-100%,0,0)
    .food-content
      position: relative
      .image-header
        position: relative
        width: 100%
        height: 0
        padding-top: 100%
        img
          position: absolute
          top: 0
          left: 0
          width: 100%
          height: 100%
        .back
          position: absolute
          top: 10px
          left: 0
          .icon-arrow_lift
            display: block
            padding: 10px
            font-size: 20px
            color: #fff
      .content
        padding: 18px
        position: relative
        .name
          font-size: 14px
          font-weight: 700
          line-height: 24px
          color: rgb(7,17,27)
          margin-bottom: 8px
        .detail
          font-size: 10px
          color: rgb(147,153,159)
          line-height: 10px
          .sell-count
            margin-right: 12px
        .price
          margin: 18px 0
          font-weight: 700
          line-height: 24px
          .now
            font-size: 14px
            color: rgb(240,20,20)
            margin-right: 8px
          .old
            font-size: 10px
            color: rgb(147,153,159)
            text-decoration line-through

        .cartControl-wrapper
          position: absolute
          right: 12px
          bottom: 12px
        .buy
          position: absolute
          right: 18px
          bottom: 18px
          z-index: 10
          line-height: 24px
          padding: 0 12px
          box-sizing: border-box
          font-size: 10px
          color: #fff
          border-radius: 12px
          background: rgb(0,160,220)
          &.fade-enter-active,&.fade-leave-active
            opacity: 1
            transition: all 0.3s
          &.fade-enter,&.fade-leave
            opacity: 0

      .info
        padding: 18px
        .title
          font-size: 14px
          color: rgb(7,17,27)
          margin-bottom: 6px
          line-height: 14px
        .text
          padding-left: 8px
          font-size: 12px
          font-weight: 200
          color: rgb(77,85,93)
          line-height: 24px
      .rating
        padding-top: 18px
        .title
          margin: 0 18px 12px
          font-size: 14px
          color: rgb(7,17,27)
          line-height: 12px
        .rating-wrapper
          padding: 0 18px

          .rating-item
            padding: 16px 0
            position: relative
            overflow: hidden
            .user
              position: absolute
              top: 16px
              right: 0
              font-size: 0
              line-height: 12px
              .name
                margin-right: 6px
                display: inline-block
                vertical-align: top
                font-size: 10px
                color: rgb(147,153,159)
            .time
              font-size: 10px
              color: rgb(147,153,159)
              line-height: 12px
            .text
              margin-top: 6px
              font-size: 12px
              line-height: 16px
              color: rgb(7,17,27)
              .icon-thumb_up,.icon-thumb_down
                margin-right: 4px
                line-height: 16px
                font-size: 12px
              .icon-thumb_up
                color: rgb(0,160,220)
              .icon-thumb_down
                color: rgb(147,153,159)


        .no-rating
          padding: 16px 0
          font-size: 12px
          color: rgb(147,153,159)
</style>
