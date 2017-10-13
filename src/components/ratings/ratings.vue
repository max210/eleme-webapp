<template lang="html">
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
      <ratingselect :select-type="selectType" :only-content="onlyContent" :ratings="ratings" @ratingfuc="getratingfuc" @contentfuc="getcontentfuc"></ratingselect>
      <div class="rating-wrapper">
        <ul>
          <li class="rating-item" v-for="rating in ratings" v-show="needShow(rating.rateType, rating.text)">
            <div class="avatar">
              <img width="28" height="28" :src="rating.avatar" alt="">
            </div>
            <div class="content">
              <h1 class="name">{{rating.username}}</h1>
              <div class="star-wrapper">
                <star :size="24" :score="rating.score"></star>
                <span class="delivery" v-show="rating.deliveryTime">{{rating.deliveryTime}}</span>
                <p class="text">{{rating.text}}</p>
                <div class="time">{{rating.rateTime | formatDate}}</div>
              </div>
            </div>
          </li>
        </ul>
      </div>
    </div>
  </div>
</template>

<script>
import BScroll from 'better-scroll'
import star from 'components/star/star'
import {formatDate} from 'common/js/date.js'
import split from 'components/split/split'
import ratingselect from 'components/ratingselect/ratingselect'

const all = 2
const errok = 0

export default {
  props: {
    seller: {
      type: Object
    }
  },
  data () {
    return {
      ratings: [],
      selectType: all,
      onlyContent: true
    }
  },
  methods: {
    getratingfuc (type) {
      this.selectType = type
      this.$nextTick(() => {
        this.scroll.refresh()
      })
    },
    getcontentfuc (onlyContent) {
      this.onlyContent = !onlyContent
      this.$nextTick(() => {
        this.scroll.refresh()
      })
    },
    needShow (type, text) {
      if (this.onlyContent && !text) {
        return false
      }
      if (this.selectType === all) {
        return true
      } else {
        return type === this.selectType
      }
    }
  },
  filters: {
    formatDate (time) {
      let date = new Date(time)
      return formatDate(date, 'yyyy-MM-dd hh:mm')
    }
  },
  components: {
    star,
    split,
    ratingselect
  },
  created () {
    this.$http.get('/api/ratings').then(response => {
      this.ratingsOriginal = response.body
      if (this.ratingsOriginal.errno === errok) {
        this.ratings = this.ratingsOriginal.data
        this.$nextTick(() => {
          this.scroll = new BScroll(this.$refs.ratings, {
            click: true
          })
        })
      }
    }, response => {

    })
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
.ratings {
  position: absolute;
  top: 174px;
  bottom: 0;
  left: 0;
  overflow: hidden;
  width: 100%;
  .overview {
    display: flex;
    padding: 20px 0;
    .overview-left {
      flex: 0 0 130px;
      width: 130px;
      padding: 12px 0;
      margin-bottom: 10px;
      border-right: 1px solid rgba(7, 17, 27, .1);
      text-align: center;
      @media only screen and (max-width: 320px) {
        flex: 0 0 110px;
        width: 110px;
      }
      .score {
        margin-top: 0;
        line-height: 28px;
        font-size: 24px;
        color: rgb(255, 153, 0);
        margin-bottom: 6px;
      }
      .title {
        margin-bottom: 8px;
        line-height: 12px;
        font-size: 12px;
        color: rgb(7, 17, 27);
      }
      .rank {
        line-height: 10px;
        font-size: 10px;
        color: rgb(147, 153, 159);
      }
    }
    .overview-right {
      flex: 1;
      padding: 6px 0 6px 24px;
      @media only screen and (max-width: 320px) {
        padding-left: 6px
      }
      .score-wrapper {
        margin-bottom: 8px;
        line-height: 18px;
        font-size: 0;
        .title {
          display: inline-block;
          vertical-align: top;
          font-size: 12px;
          color: rgb(7, 17, 27);
        }
        .star {
          display: inline-block;
          vertical-align: top;
          margin: 0 12px;
        }
        .score {
          display: inline-block;
          vertical-align: top;
          line-height: 18px;
          font-size: 12px;
          padding-left: 5px;
          color: rgb(255, 153, 0);
          @media only screen and (max-width: 320px) {
            padding-left: 0;
          }
        }
      }
      .delivery-wrapper {
        font-size: 0;
        .title {
          font-size: 12px;
          color: rgb(7, 17, 27);
        }
        .delivery {
          margin-left: 12px;
          font-size: 12px;
          color: rgb(147, 153, 159);
        }
      }
    }
  }
  .rating-wrapper {
    padding: 0 18px;
    .rating-item {
      display: flex;
      padding: 18px 0;
      @include border-1px(rgba(7,17,27,.1));
      .avatar {
        flex: 0 0 28px;
        width: 28px;
        margin-right: 20px;
        img {
          border-radius: 50%;
        }
      }
      .content {
        position: relative;
        flex: 1;
        .name {
          margin-bottom: 4px;
          font-size: 12px;
          line-height: 10px;
          color: rgb(7, 17, 27);
        }
        .star-wrapper {
          margin-bottom: 6px;
          font-size: 0;
          .star {
            display: inline-block;
            margin-right: 6px;
            vertical-align: top;
          }
          .delivery {
            display: inline-block;
            margin-right: 6px;
            vertical-align: top;
            font-size: 12px;
            line-height: 10px;
            color: rgb(147, 153, 159);
          }
          .text {
            padding-bottom: 6px;
            font-size: 12px;
            line-height: 18px;
            color: rgb(7, 17, 27);
          }
          .time {
            position: absolute;
            bottom: 0;
            left: 0;
            line-height: 12px;
            font-size: 10px;
            color: rgb(7, 17, 27);
          }
        }
      }
    }
  }
}
</style>
