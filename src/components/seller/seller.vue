<template lang="html">
  <div class="seller" ref="seller">
    <div class="seller-content">
      <div class="overview">
        <h1 class="title">{{seller.name}}</h1>
        <div class="desc border-1px">
          <star :size="36" :score="seller.score"></star>
          <span class="text">（{{seller.ratingCount}}）</span>
          <span class="text">月售{{seller.sellCount}}单</span>
        </div>
        <ul class="remark">
          <li>
            <h2>起送价</h2>
            <div class="content">
              <span class="stress">{{seller.minPrice}}</span>元
            </div>
          </li>
          <li>
            <h2>商家配送</h2>
            <div class="content">
              <span class="stress">{{seller.deliveryPrice}}</span>元
            </div>
          </li>
          <li>
            <h2>平均配送时间</h2>
            <div class="content">
              <span class="stress">{{seller.deliveryTime}}</span>分钟
            </div>
          </li>
        </ul>
        <div class="favorite" @click="toggleFavorite">
          <span class="icon-favorite" :class="{'active' : favorite}"></span>
          <span class="text">{{favoriteText}}</span>
        </div>
      </div>
      <split></split>
      <div class="bulletin">
        <h1 class="title">公告与活动</h1>
        <div class="content-wrapper border-1px">
          <p class="content">{{seller.bulletin}}</p>
        </div>
        <div class="supports" v-if="seller.supports">
          <li class="support-item" v-for="(item, index) in seller.supports">
            <span class="icon" :class="classMap[seller.supports[index].type]"></span>
            <span class="text">{{seller.supports[index].description}}</span>
          </li>
        </div>

      </div>
      <split></split>
      <div class="pics">
        <h1 class="title">商家实景</h1>
        <div class="pic-wrapper" refs="picWrapper">
          <ul class="pic-list" refs="picList">
            <li class="pic-item" v-for="pic in seller.pics">
              <img :src="pic" width="120" height="90" alt="">
            </li>
          </ul>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import star from 'components/star/star'
import split from 'components/split/split'
import BScroll from 'better-scroll'

export default {
  props: {
    seller: {
      type: Object
    }
  },
  watch: {
    'seller' () {
      this._initScroll()
      // this._initPics()
    }
  },
  mounted () {
    this._initScroll()
    // this._initPics()
  },
  computed: {
    favoriteText () {
      return this.favorite ? '已收藏' : '收藏'
    }
  },
  data () {
    return {
      favorite: true
    }
  },
  created () {
    this.classMap = ['decrease', 'discount', 'special', 'invoice', 'guarantee']
  },
  methods: {
    _initScroll () {
      if (!this.scroll) {
        this.scroll = new BScroll(this.$refs.seller, {
          click: true
        })
      }
    },
    toggleFavorite () {
      this.favorite = !this.favorite
    }
    // _initPics () {
    //   if (this.seller.pics) {
    //     let picWidth = 120
    //     let margin = 6
    //     let width = (picWidth + margin) * this.seller.pics.length - margin
    //     this.$refs.picList.style.width = width + 'px'
    //     console.log(1)
    //     this.$nextTick(() => {
    //       if (!this.scroll) {
    //         this.picScroll = new BScroll(this.$refs.picWrapper, {
    //           scrollX: true,
    //           eventPassthrough: 'vertical'
    //         })
    //       } else {
    //         this.picScroll.refresh()
    //       }
    //     })
    //   }
    // }
  },
  components: {
    star,
    split
  }
}
</script>

<style lang="scss">
@import "../../common/scss/common";

li {
  list-style: none;
}
ul {
  padding: 0;
  margin: 0;
}
.seller {
  position: absolute;
  top: 174px;
  bottom: 0;
  left: 0;
  width: 100%;
  overflow: hidden;
  .overview {
    padding: 18px;
    .title {
      margin-bottom: 8px;
      line-height: 14px;
      font-size: 14px;
      color: rgb(7, 17, 27);
    }
    .desc {
      padding: 18px 18px 18px 0;
      font-size: 0;
      @include border-1px(rgba(7,17,27,.1));
      .star {
        display: inline-block;
        vertical-align: top;
      }
      .text {
        margin: 0 12px;
        line-height: 18px;
        display: inline-block;
        vertical-align: top;
        color: rgb(77, 85, 93);
        font-size: 10px;
      }
    }
    .remark {
      display: flex;
      padding-top: 18px;
      li {
        flex: 1;
        text-align: center;
        border-right: 1px solid rgba(7, 17, 27, .1);
        h2 {
          margin-bottom: 4px;
          line-height: 10px;
          font-size: 10px;
          color: rgb(147, 153, 159);
        }
        .content {
          line-height: 24px;
          color: rgb(7, 17, 27);
          .stress {
            font-size: 24px;
          }
        }
      }
      & :last-child {
        border-right: none;
      }
    }
    .favorite {
      position: absolute;
      right: 18px;
      top: 18px;
      width: 40px;
      text-align: center;
      .icon-favorite {
        display: block;
        margin-bottom: 4px;
        color: #d4d6d9;
        line-height: 24px;
        font-size: 24px;
        &.active {
          color: rgb(255, 20, 20);
        }
      }
      .text {
        line-height: 10px;
        font-size: 10px;
        color: rgb(77, 85, 93);
      }
    }
  }
  .bulletin {
    padding: 18px 18px 0 18px;
    .title {
      margin-bottom: 8px;
      line-height: 14px;
      font-size: 14px;
      color: rgb(7, 17, 27);
    }
    .content-wrapper {
      padding: 0 12px 16px 12px;
      @include border-1px(rgba(7, 17, 27, .1));
      .content {
        line-height: 24px;
        font-size: 12px;
        color: rgb(240, 20, 20);
      }
    }
    .supports {
      .support-item {
        padding: 16px 12px;
        @include border-1px(rgba(7, 17, 27, .1));
        & :last-child {
          border: none;
        }
        .icon {
          display: inline-block;
          width: 16px;
          height: 16px;
          vertical-align: top;
          margin-right: 6px;
          background-size: 16px 16px;
          background-repeat: no-repeat;
          &.decrease {
            @include bg-image('decrease_4');
          }
          &.discount {
            @include bg-image('discount_4');
          }
          &.guarantee {
            @include bg-image('guarantee_4');
          }
          &.invoice {
            @include bg-image('invoice_4');
          }
          &.special {
            @include bg-image('special_4');
          }
        }
        .text {
          line-height: 18px;
          color: rgb(7, 17, 27);
        }
      }
    }
  }
  .pics {
    padding: 18px;
    .title {
      margin-bottom: 12px;
      line-height: 14px;
      font-size: 14px;
      color: rgb(7, 17, 27);
    }
    .pic-wrapper {
      width: 100%;
      overflow: hidden;
      white-space: nowrap;
      .pic-list {
        font-size: 0;
        & :last-child {
          margin-right: 0;
        }
        .pic-item {
          display: inline-block;
          margin-right: 6px;
          width: 120px;
          height: 90px;
        }
      }
    }
  }
}
</style>
