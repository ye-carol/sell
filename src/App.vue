<template>
  <div id="app" class="app">
    <v-header :seller="seller"></v-header>
    <div class="tab border-1px">
      <div class="tab-item active">
        <router-link :to="{name: 'goods'}">商品</router-link>
      </div>
      <div class="tab-item">
        <router-link :to="{name: 'ratings'}">评价</router-link>
      </div>
      <div class="tab-item">
        <router-link :to="{name: 'seller'}">商家</router-link>
      </div>
    </div>
    <keep-alive>
      <router-view :seller="seller"/>
    </keep-alive>

  </div>
</template>

<script>
  import header from 'components/header/header'
  import {urlParse} from 'common/js/util'
  const ERR_OK = 0
export default {
  name: 'app',
  data () {
    return {
      seller: {
        id: (() => {
          let queryParam = urlParse()
          return queryParam.id
        })()
      }
    }
  },
  mounted () {
    this.$nextTick(() => {
      this.$http.get('/api/seller?id=' + this.seller.id).then((response) => {
        response = response.body
        if (response.errno === ERR_OK) {
          this.seller = response.data
          this.seller = Object.assign({},this.seller,response.data)
        }
      })
    })
  },
  components: {
    'v-header': header
  }
}
</script>

<style  lang="stylus" rel="stylesheet/stylus">
  @import "./common/stylus/mixin.styl"
  .app
    .tab
      display: flex
      width: 100%
      height: 40px
      line-height: 40px
      background-color: #ffffff
      border-1px(rgba(7,17,27,0.1))
      .tab-item
        flex: 1
        text-align: center
        font-size: 14px
        & a
          display: block
          font-size: 14px
          color: rgb(77,85,93)
          &.active
            color: rgb(240,20,20)

</style>
