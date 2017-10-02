<template lang="html">
  <div class="cartcontrol">
    <transition name="move">
      <div class="cart-decrease icon-remove_circle_outline" v-show="food.count>0" @click="decreaseCart" transition="fade"></div>
    </transition>
    <div class="cart-count" v-show="food.count>0">{{food.count}}</div>
    <div class="cart-add icon-add_circle" @click="addCart"></div>
  </div>
</template>

<script>
import Vue from 'vue'

export default {
  props: {
    food: {
      type: Object
    }
  },
  created () {
  },
  methods: {
    addCart () {
      if (!this.food.count) {
        Vue.set(this.food, 'count', 1)
      } else {
        this.food.count++
      }
    },
    decreaseCart () {
      this.food.count--
    }
  }
}
</script>

<style lang="scss">
  .cartcontrol {
    font-size: 0;
    .cart-decrease, .cart-add {
      display: inline-block;
      padding: 6px;
      font-size: 24px;
      line-height: 24px;
      color: rgb(0, 160, 220);
    }
    .cart-count {
      display: inline-block;
      font-size: 10px;
      width: 12px;
      vertical-align: top;
      padding-top: 6px;
      line-height: 24px;
      text-align: center;
      color: rgb(147, 153, 159);
    }
    .cart-decrease {
      transition: all .4s;
      opacity: 1;
      transform: translate(0, 0);
      &.move-enter, &.move-leave-to {
        opacity: 0;
        transform: translate(24px, 0);
      }
    }

  }
</style>
