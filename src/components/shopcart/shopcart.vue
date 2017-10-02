<template lang="html">
  <div class="shopcart">
    <div class="content">

      <div class="content-left">
        <div class="logo-wrapper">
          <div class="logo">
            <i class="icon-shopping_cart"></i>
          </div>
          <div class="num">

          </div>
        </div>
        <div class="price">￥{{totalPrice}}</div>
        <div class="desc">另需配送费￥{{seller.deliveryPrice}}元</div>
      </div>

      <div class="content-right">
        <div class="pay">
          ￥{{seller.minPrice}}元起送
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  props: {
    seller: {
      type: Object
    },
    selectfoods: {
      type: Array,
      default () {
        return []
      }
    }
  },
  computed: {
    totalPrice () {
      let total = 0
      this.selectfoods.forEach((food) => {
        total += food.price * food.count
      })
      return total
    },
    totalCount () {
      let count = 0
      this.selectfoods.forEach((food) => {
        count += food.count
      })
      return count
    }
  },
  data () {
    return {
      dd: []
    }
  }
}
</script>

<style lang="scss">
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
          .icon-shopping_cart {
            font-size: 24px;
            color: #80858a;
            line-height: 44px;
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
        background: #2b333b;
      }
    }
  }
}
</style>
