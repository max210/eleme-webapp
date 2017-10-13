<template>
  <div id="app">
    <v-header :seller="seller"></v-header>
    <div class="tab border-1px">
      <div class="tab-item"><router-link :to="{name: 'Goods'}" class="btn">商品</router-link></div>
      <div class="tab-item"><router-link :to="{name: 'Ratings'}" class="btn">评论</router-link></div>
      <div class="tab-item"><router-link :to="{name: 'Seller'}" class="btn">商家</router-link></div>
    </div>
  <keep-alive><router-view :seller="seller"></router-view></keep-alive>
  </div>
</template>

<script>
import Header from 'components/header/header'
import 'common/fontsFolder/icon.scss'

const errok = 0

export default {
  name: 'app',
  components: {
    'v-header': Header
  },
  data () {
    return {
      seller: {}
    }
  },
  created () {
    this.$http.get('/api/seller').then(response => {
      this.sellerOriginal = response.body
      if (this.sellerOriginal.errno === errok) {
        this.seller = this.sellerOriginal.data
      }
    }, response => {

    })
  }
}
</script>

<style lang="scss">
  @import "common/scss/common.scss";

  .tab {
    position: relative;
    display: flex;
    width: 100%;
    height: 40px;
    line-height: 40px;
    flex-direction: row;
    @include border-1px(rgba(7, 17, 27, 0.1));
    .tab-item {
      flex: 1;
      text-align: center;
      .btn {
        text-decoration: none;
        display: block;
        font-size: 14px;
        color: rgb(77,85,93);
      }
    }
  }
.tab .tab-item .router-link-active {
    color: rgb(240,20,20);
  }
</style>
