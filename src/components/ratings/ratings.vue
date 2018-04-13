<template>
  <div class="ratings" ref="ratings">
      <div class="ratings-content">
        <div class="overview">
          <div class="overview-left">
            <h1 class="score">{{seller.score}}</h1>
            <div class="title">综合评分</div>
            <div class="rank">高于周边商家{{seller.rankRate}}</div>
          </div>
          <div class="overview-right">
            <div class="score-wrapper">
              <span class="title">服务态度</span>
              <star :size="36" :score="seller.serviceScore"></star>
              <span class="score">{{seller.serviceScore}}</span>
            </div>
            <div class="score-wrapper">
              <span class="title">商品评分</span>
              <star :size="36" :score="seller.foodScore"></star>
              <span class="score">{{seller.foodScore}}</span>
            </div>
            <div class="delivery-wrapper">
              <span class="title">送达时间</span>
              <span class="delivery">{{seller.deliveryTime}}分钟</span>
            </div>
          </div>
        </div>
        <split></split>
        <ratingSelect @select="selectRating" @toggle="toggle" :ratings="ratings" :select-type="selectType" :only-content="onlyContent" :desc="desc"></ratingSelect>
        <div class="ratings-wrapper">
          <ul>
            <li v-show="needShow(rating.rateType,rating.text)" v-for="rating in ratings" class="rating-item">
              <div class="avatar"><img :src="rating.avatar" alt="" width="28px"></div>
              <div class="content">
                <div class="name">{{rating.username}}</div>
                <div class="star-wrapper">
                  <star :size="24" :score="rating.score"></star>
                  <div class="delivery">{{rating.deliveryTime}}分钟送达</div>
                </div>
                <div class="text">{{rating.text}}</div>
                <div class="time">{{rating.rateTime | formatDate}}</div>
                <div class="recommend">
                  <i :class="{'icon-thumb_up':rating.rateType===0, 'icon-thumb_down': rating.rateType===1 }"></i>
                  <span v-for="item in rating.recommend" class="recommend-item">{{item}}</span>
                </div>
              </div>
            </li>
          </ul>
        </div>
      </div>

  </div>
</template>

<script type="text/ecmascript-6">
  import BScroll from 'better-scroll'
  import star from 'components/star/star'
  import split from 'components/split/split'
  import ratingSelect from 'components/ratingSelect/ratingSelect'
  import {formatDate} from 'common/js/date'

  const ERR_OK = 0
  const POSITIVE = 0
  const NEGATIVE = 1
  const ALL = 2

  export default {
    name: 'ratings',
    props: {
      seller: {
        type: Object
      }
    },
    data () {
      return {
        ratings: [],
        selectType: ALL,
        onlyContent: true,
        desc: {
          all: '全部',
          positive: '满意',
          negative: '不满意'
        }
      }


    },
    mounted () {
      this.$http.get('/api/ratings').then((response) => {
        response = response.body
        if (response.errno === ERR_OK) {
          this.ratings = response.data
          this.$nextTick(() => {
            this.scroll = new BScroll(this.$refs.ratings, {
              click: true
            })
          })
        }
      })

    },
    methods: {
      needShow (type,text) {
        if(this.onlyContent && !text ) {
          return false
        }
        if(this.selectType === ALL) {
          return true
        }else{
          return this.selectType === type
        }
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
      }
    },
    components: {
      star: star,
      split: split,
      ratingSelect: ratingSelect
    },
    filters: {
      formatDate(time) {
        let date = new Date(time);
        return formatDate(date, 'yyyy-MM-dd hh:mm');
      }
    },
  }
</script>

<style lang="stylus" rel="stylesheet/stylus">
  .ratings
    position: absolute
    top: 174px
    bottom: 0
    left: 0
    width: 100%
    overflow: hidden
    .overview
      display: flex
      padding: 18px 0
      .overview-left
        flex: 0 0 137px
        width: 137px
        padding: 6px 0
        text-align: center
        border-right: 1px solid rgba(7,17,27,0.1)
        @media only screen and (max-width: 320px)
          flex: 0 0 120px
          width: 120px
        .score
          margin-bottom: 6px
          font-size: 24px
          line-height: 28px
          color: rgb(255,153,0)
        .title
          line-height: 12px
          margin-bottom: 8px
          font-size: 12px
          color: rgb(7,17,27)
        .rank
          line-height: 10px
          font-size: 10px
          color: rgb(147,153,159)
      .overview-right
        flex: 1
        padding-left: 24px
        @media only screen and (max-width: 320px)
          padding-left 6px
        .score-wrapper
          margin-bottom: 8px
          font-size: 0
          .title
            display: inline-block
            line-height: 18px
            vertical-align: top
            font-size: 12px
            color: rgb(7,17,27)
          .star
            display: inline-block
            margin: 0 12px
            vertical-align: top
            @media only screen and (max-width: 320px)
              margin:0 8px
          .score
            display: inline-block
            line-height: 18px
            vertical-align: top
            font-size: 12px
            color: rgb(255,153,0)
        .delivery-wrapper
          font-size: 0
          .title
            line-height: 18px
            font-size: 12px
            color: rgb(7,17,27)
          .delivery
            margin-left: 12px
            font-size: 12px
            color: rgb(147,153,159)
    .ratings-wrapper
      padding: 0 18px
      .rating-item
        display: flex
        padding: 18px 0
        .avatar
          flex: 0 0 28px
          width: 28px
          margin-right: 12px
          img
            border-radius: 50%
        .content
          position: relative
          flex: 1
          .name
            margin-bottom: 4px
            font-size: 12px
            color: rgb(7,17,27)
            line-height: 12px
          .star-wrapper
            margin: 0 6px 6px 0
            .star
              display: inline-block
              vertical-align: top
            .delivery
              display:inline-block
              font-size: 10px
              font-weight: 200
              color: rgb(147,153,159)
              line-height: 12px
          .text
            margin-bottom: 8px
            font-size: 12px
            color: rgb(7,17,27)
            line-height: 18px
          .recommend
            line-height: 16px
            font-size: 0
            .icon-thumb_up,icon-thumb_down
              display: inline-block
              margin: 0 8px 4px 0
              font-size: 9px
            .icon-thumb_up
              color: rgb(0,160,220)
            .icon-thumb_down
              color: rgb(189,187,191)
            .recommend-item
              display: inline-block
              padding: 0 6px
              margin: 0 8px 4px 0
              font-size: 9px
              line-height: 16px
              color: rgb(147,153,159)
              border: 1px solid rgba(7,17,27,0.1)
              border-radius: 2px
              background: #ffffff
          .time
            position: absolute
            top: 0
            right: 0
            font-size: 10px
            font-weight: 200
            color: rgb(147,153,159)
            line-height: 12px

</style>
