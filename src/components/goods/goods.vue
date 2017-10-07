<template lang="html">
  <div class="goods">
    <div class="menu-wrapper" ref="menuWrapper">
      <ul>
        <li class="menu-item" v-for="(item, index) in goods" :class="{'current' :currentIndex===index}" @click="selectMenu(index)">
          <span class="text border-1px">
            <span class="icon" v-show="item.type > 0" :class="classMap[item.type]"></span>
            {{ item.name }}
          </span>
        </li>
      </ul>
    </div>

    <div class="foods-wrapper" ref="foodsWrapper">
      <ul>
        <li v-for="item in goods" class="food-list food-list-hook">
          <h1 class="title">{{ item.name }}</h1>
          <ul>
            <li v-for="food in item.foods" class="food-item border-1px" @click="selectFood(food)">
              <div class="icon">
                <img :src="food.icon" width="57" height="57" alt="">
              </div>
              <div class="content">
                <h2 class="name">{{ food.name }}</h2>
                <p class="desc">{{ food.description }}</p>
                <div class="extra">
                  <span class="count">月售{{ food.sellCount }}</span>
                  <span>好评率{{ food.rating }}%</span>
                </div>
                <div class="price">
                  <span class="now">￥{{ food.price }}</span><span class="old" v-show="food.oldPrice">￥{{food.oldPrice}}</span>
                </div>
                <div class="cartcontrol-wrapper">
                  <cartcontrol :food="food"></cartcontrol>
                </div>
              </div>
            </li>
          </ul>
        </li>
      </ul>
    </div>
    <shopcart :select-foods="selectFoods" :seller="seller"></shopcart>

    <food :food="selectedFood" ref="foodRef"></food>
  </div>
</template>

<script>
import BScroll from 'better-scroll'
import shopcart from 'components/shopcart/shopcart'
import cartcontrol from 'components/cartcontrol/cartcontrol'
import food from 'components/food/food'

const errok = 0

export default {
  components: {
    shopcart,
    cartcontrol,
    food
  },
  data () {
    return {
      goods: [],
      listHeight: [],
      seller: {},
      scrollY: 0,
      selectedFood: {}
    }
  },
  computed: {
    currentIndex () {
      for (let i = 0; i < this.listHeight.length; i++) {
        let height1 = this.listHeight[i]
        let height2 = this.listHeight[i + 1]
        if (!height2 || (this.scrollY >= height1 && this.scrollY < height2)) {
          return i
        }
      }
      return 0
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
  created () {
    this.classMap = ['decrease', 'discount', 'special', 'invoice', 'guarantee']

    this.$http.get('/api/goods').then(response => {
      this.goodsOriginal = response.body
      if (this.goodsOriginal.errno === errok) {
        this.goods = this.goodsOriginal.data
        this.$nextTick(() => {
          this._initScroll()
          this._calulateHeight()
        })
      }
    }, response => {
    })
    this.$http.get('/api/seller').then(response => {
      this.sellerOriginal = response.body
      if (this.sellerOriginal.errno === errok) {
        this.seller = this.sellerOriginal.data
      }
    }, response => {
    })
  },
  methods: {
    selectMenu (index) {
      let foodList = this.$refs.foodsWrapper.getElementsByClassName('food-list-hook')
      let el = foodList[index]
      this.foodsScroll.scrollToElement(el, 300)
    },
    selectFood (food) {
      this.selectedFood = food
      this.$refs.foodRef.show()
    },
    _initScroll () {
      this.menuScroll = new BScroll(this.$refs.menuWrapper, {
        click: true
      })
      this.foodsScroll = new BScroll(this.$refs.foodsWrapper, {
        click: true,
        probeType: 3
      })

      this.foodsScroll.on('scroll', (pos) => {
        this.scrollY = Math.abs(Math.round(pos.y))
      })
    },
    _calulateHeight () {
      let foodList = this.$refs.foodsWrapper.getElementsByClassName('food-list-hook')
      let height = 0
      this.listHeight.push(height)
      for (let i = 0; i < foodList.length; i++) {
        let item = foodList[i]
        height += item.clientHeight
        this.listHeight.push(height)
      }
    }
  }
}
</script>

<style lang="scss">
@import "../../common/scss/common";

ul, li{
  margin: 0;
  padding: 0;
  list-style: none;
}
.goods {
  position: absolute;
  width: 100%;
  top: 174px;
  bottom: 46px;
  display: flex;
  overflow: hidden;
  .menu-wrapper {
    flex: 0 0 80px;
    width: 80px;
    background: #f3f5f7;
    .menu-item {
      display: table;
      height: 54px;
      width: 56px;
      line-height: 14px;
      padding: 0 12px;
      &.current {
        font-weight: 700;
        position: relative;
        margin-top: -1px;
        z-index: 10;
        background: #fff;
        .text {
          @include border-none();
        }
      }
      .icon {
        display: inline-block;
        width: 12px;
        height: 12px;
        line-height: 12px;
        margin-right: 2px;
        background-size: 12px 12px;
        background-repeat: no-repeat;
        &.decrease {
          @include bg-image('decrease_3');
        }
        &.discount {
          @include bg-image('discount_3');
        }
        &.guarantee {
          @include bg-image('guarantee_3');
        }
        &.invoice {
          @include bg-image('invoice_3');
        }
        &.special {
          @include bg-image('special_3');
        }
      }
      .text {
        font-size: 12px;
        display: table-cell;
        vertical-align: middle;
        width: 56px;
        @include border-1px(rgba(7,17,27,.1));
      }
    }
  }
  .foods-wrapper {
    flex: 1;
    .title {
      padding-left: 14px;
      height: 26px;
      line-height: 26px;
      border-left: 2px solid #d9dde1;
      font-size: 12px;
      color: rgb(147,153,159);
      background: #f3f5f7;
    }
    .food-item {
      display: flex;
      margin: 18px;
      padding-bottom: 18px;
      @include border-1px(rgba(7,17,27,.1));
      &:last-child {
        @include border-none;
        margin-bottom: 0;
      }
      .icon {
        flex: 0 0 57;
        margin-right: 10px;
      }
      .content {
        flex: 1;
        .name {
          font-size: 14px;
          margin: 2px 0 8px 0;
          height: 14px;
          line-height: 14px;
          color: rgb(7,17,27);
        }
        .desc, .extra {
          font-size: 10px;
          line-height: 10px;
          color: rgb(147,153,159);
        }
        .desc {
          line-height: 12px;
          margin-bottom: 8px;
        }
        .extra {
          .count {
            margin-right: 12px;
          }
        }
        .price {
          font-weight: 700;
          line-height: 24px;
          .now {
            margin-right: 8px;
            font-size: 14px;
            color: rgb(240,20,20);
          }
          .old {
            text-decoration: line-through;
            font-size: 10px;
            color: rgb(147,153,159);
          }
        }
        .cartcontrol-wrapper {
          position: absolute;
          right: 0;
          bottom: 12px;
        }
      }
    }
  }
}
</style>
