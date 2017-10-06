<template lang="html">
  <div>
    <div class="shopcart">

      <transition name="fold">
        <div class="shopcart-list" v-show="listShow">
        <div class="list-header">
          <h1 class="title">购物车</h1>
          <span class="empty" @click="empty">清空</span>
        </div>
        <div class="list-content">
          <ul>
            <li class="food" v-for="food in selectFoods">
              <span class="name">{{food.name}}</span>
              <div class="price">
                <span>￥{{food.price * food.count}}</span>
              </div>
              <div class="cartcontrol-wrapper">
                <cartcontrol :food="food"></cartcontrol>
              </div>
            </li>
          </ul>
        </div>
      </div>
      </transition>

      <div class="content">

        <div class="content-left">
          <div class="logo-wrapper" @click="toggleList">
            <div class="logo" :class="{'highlight' :totalCount>0}">
              <i class="icon-shopping_cart" :class="{'highlight' :totalCount>0}"></i>
            </div>
            <div class="num" v-show="totalCount>0">{{totalCount}}</div>
          </div>
          <div class="price" :class="{'highlight' :totalPrice>0}">￥{{totalPrice}}</div>
          <div class="desc">另需配送费￥{{seller.deliveryPrice}}元</div>
        </div>

        <div class="content-right" @click="pay">
          <div class="pay" :class="payClass">
            {{payDesc}}
          </div>
        </div>
      </div>

    </div>
    <transition name="fade">
      <div class="list-mask" v-show="listShow" @click="hideList"></div>
    </transition>
  </div>
</template>

<script>
import cartcontrol from 'components/cartcontrol/cartcontrol'

export default {
  components: {
    cartcontrol
  },
  props: {
    seller: {
      type: Object
    },
    selectFoods: {
      type: Array,
      default () {
        return []
      }
    }
  },
  computed: {
    totalPrice () {
      let total = 0
      this.selectFoods.forEach((food) => {
        total += food.price * food.count
      })
      return total
    },
    totalCount () {
      let count = 0
      this.selectFoods.forEach((food) => {
        count += food.count
      })
      return count
    },
    payDesc () {
      if (this.totalPrice === 0) {
        return `￥${this.seller.minPrice}元起送`
      } else if (this.totalPrice < this.seller.minPrice) {
        let diff = this.seller.minPrice - this.totalPrice
        return `还差￥${diff}元起送`
      } else {
        return '去结算'
      }
    },
    payClass () {
      if (this.totalPrice < this.seller.minPrice) {
        return 'not-enough'
      } else {
        return 'enough'
      }
    },
    listShow () {
      if (!this.totalCount) {
        this.fold = true
        return false
      } else {
        let show = !this.fold
        return show
      }
    }
  },
  methods: {
    toggleList () {
      if (!this.totalCount) {
        return
      }
      this.fold = !this.fold
    },
    empty () {
      this.selectFoods.forEach((food) => {
        food.count = 0
      })
    },
    hideList () {
      this.fold = true
    },
    pay () {
      if (this.totalPrice < this.seller.minPrice) {
        return
      }
      alert('支付页面')
    }
  },
  data () {
    return {
      fold: true
    }
  }
}
</script>

<style lang="scss">
@import "../../common/scss/common";

.shopcart {
  position: fixed;
  left: 0;
  bottom: 0;
  z-index: 50;
  height: 48px;
  background: #000;
  width: 100%;
  font-size: 0;
  .content {
    display: flex;
    background: #141d27;
    color: rgba(255, 255, 255, .4);
    .content-left {
      flex: 1;
      .logo-wrapper {
        display: inline-block;
        position: relative;
        top: -10px;
        margin: 0 12px;
        padding: 6px;
        width: 56px;
        height: 56px;
        box-sizing: border-box;
        vertical-align: top;
        border-radius: 50%;
        background: rgb(20, 29, 39);
        .logo {
          width: 100%;
          height: 100%;
          border-radius: 50%;
          background: #2b343c;
          text-align: center;
          &.highlight {
            background: rgb(0, 160, 220);
          }
          .icon-shopping_cart {
            font-size: 24px;
            color: #80858a;
            line-height: 44px;
            &.highlight {
              color: #fff;
            }
          }
        }
        .num {
          position: absolute;
          right: 0;
          top: 0;
          width: 24px;
          height: 16px;
          line-height: 16px;
          text-align: center;
          border-radius: 16px;
          font-size: 9px;
          font-weight: 700;
          color: #fff;
          background: rgb(240, 20, 20);
          box-shadow: 0 4px 8px 0 rgba(0, 0, 0, .4);
        }
      }
      .price {
        display: inline-block;
        vertical-align: top;
        line-height: 24px;
        margin-top: 12px;
        box-sizing: border-box;
        padding-right: 12px;
        border-right: 1px solid rgba(255, 255, 255, .1);
        font-size: 16px;
        font-weight: 700px;
        &.highlight {
          color: #fff;
        }
      }
      .desc {
        display: inline-block;
        vertical-align: top;
        line-height: 24px;
        margin: 12px 0 0 12px;
        font-size: 10px;
      }
    }
    .content-right {
      float: 0 0 105px;
      width: 105px;
      .pay {
        height: 48px;
        line-height: 48px;
        text-align: center;
        font-size: 12px;
        font-weight: 700;
        &.not-enough {
          background: #2b333b;
        }
        &.enough {
          background: #00b43c;
          color: #fff;
        }
      }
    }
  }
  .shopcart-list {
    position: absolute;
    top: 0;
    left: 0;
    font-size: 16px;
    z-index: -1;
    width: 100%;
    transform: translate3d(0, -100%, 0);
    transition: all .5s;
    opacity: 1;
    &.fold-enter, &.fold-leave-to {
      transform: translate3d(0, 0, 0);
    }
    .list-header {
      height: 40px;
      line-height: 40px;
      padding: 0 18px;
      background: #f3f5f7;
      border-bottom: 1px solid rgba(7, 17, 27, .1);
      .title {
        margin: 0;
        float: left;
        font-size: 14px;
        color: rgb(7, 17, 27);
      }
      .empty {
        float: right;
        font-size: 12px;
        color: rgb(0, 160, 220);
      }
    }
    .list-content {
      width: 100%;
      padding: 18px;
      max-height: 217px;
      background: #fff;
      overflow: auto;
      .food {
        position: relative;
        padding: 12px 0;
        box-sizing: border-box;
        @include border-1px(rgba(7, 17, 27, .1));
        .name {
          line-height: 24px;
          font-size: 14px;
          color: rgb(7, 17, 27);
        }
        .price {
          position: absolute;
          right: 130px;
          bottom: 12px;
          line-height: 24px;
          font-size: 14px;
          font-weight: 700px;
          color: rgb(240, 20, 20)
        }
        .cartcontrol-wrapper {
          position: absolute;
          right: 30px;
          bottom: 6px;
        }
      }
    }
  }
}
.list-mask {
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  z-index: 40px;
  opacity: 1;
  background: rgba(7, 17, 27, .6);
  backdrop-filter: blur(5px);
  transition: all .5;
  &.fade-enter, &.fade-leave-to {
    opacity: 0;
    background: rgba(7, 17, 27, 0);
  }
}
</style>
