<template>
  <div class="cart">
    <router-link class="curr" to="/cart">
      <i class="iconfont icon-cart"></i><em>{{ $store.getters['cart/validTotal'] }}</em>
    </router-link>
    <div class="layer" v-if="$store.getters['cart/validTotal'] && $route.path !== '/cart'">
      <div class="list">
        <div class="item" v-for="item in $store.getters['cart/validList']" :key="item.id">
          <router-link :to="`/product/${item.id}`">
            <img :src="item.picture" alt="">
            <div class="center">
              <p class="name ellipsis-2">{{ item.name }}</p>
              <p class="attr ellipsis">{{ item.attrsText }}</p>
            </div>
            <div class="right">
              <p class="price">&yen;{{ item.nowPrice }}</p>
              <p class="count">x{{ item.count }}</p>
            </div>
          </router-link>
          <i class="iconfont icon-close-new" @click="deleteCart(item.skuId)"></i>
        </div>
      </div>
      <div class="foot">
        <div class="total">
          <p>共 {{ $store.getters['cart/validTotal'] }} 件商品</p>
          <p>&yen;{{ $store.getters['cart/validAmount'] }}</p>
        </div>
        <xtx-button type="plain" @click="$router.push('/cart')">去购物车结算</xtx-button>
      </div>
    </div>
  </div>
</template>

<script>
import { useStore } from 'vuex'
import Message from '@/components/library/Message'

export default {
  name: 'HeaderCart',
  setup () {
    const store = useStore()
    store.dispatch('cart/findCart')
    // 删除购物车商品
    const deleteCart = (skuId) => {
      store.dispatch('cart/deleteCart', skuId).then(() => {
        Message({ type: 'success', text: '删除成功' })
      }).catch(e => {
        Message({ type: 'error', text: '删除失败' })
      })
    }
    return { deleteCart }
  }
}
</script>

<style scoped lang="less">
.cart {
  width: 50px;
  position: relative;
  z-index: 600;
  .curr {
    height: 32px;
    line-height: 32px;
    text-align: center;
    position: relative;
    display: block;
    .icon-cart {
      font-size: 22px;
    }
    em {
      font-style: normal;
      position: absolute;
      right: 0;
      top: 0;
      padding: 1px 6px;
      line-height: 1;
      background: @helpColor;
      color: #fff;
      font-size: 12px;
      border-radius: 10px;
      font-family: Arial;
    }
  }
  &:hover {
    .layer {
      opacity: 1;
      transform: none
    }
  }
  .layer {
    opacity: 0;
    transition: all .4s .2s;
    transform: translateY(-200px) scale(1, 0);
    width: 400px;
    height: 400px;
    position: absolute;
    top: 50px;
    right: 0;
    box-shadow: 0 0 10px rgba(0,0,0,0.2);
    background: #fff;
    border-radius: 4px;
    padding-top: 10px;
    &::before {
      content: "";
      position: absolute;
      right: 14px;
      top: -10px;
      width: 20px;
      height: 20px;
      background: #fff;
      transform: scale(0.6,1) rotate(45deg);
      box-shadow: -3px -3px 5px rgba(0,0,0,0.1);
    }
    .foot {
      position: absolute;
      left: 0;
      bottom: 0;
      height: 70px;
      width: 100%;
      padding: 10px;
      display: flex;
      justify-content: space-between;
      background: #f8f8f8;
      align-items: center;
      .total {
        padding-left: 10px;
        color: #999;
        p {
          &:last-child {
            font-size: 18px;
            color: @priceColor;
          }
        }
      }
    }
  }
  .list {
    height: 310px;
    overflow: auto;
    padding: 0 10px;
    &::-webkit-scrollbar{
      width:10px;
      height:10px;
    }
    &::-webkit-scrollbar-track{
      background: #f8f8f8;
      border-radius: 2px;
    }
    &::-webkit-scrollbar-thumb{
      background: #eee;
      border-radius:10px;
    }
    &::-webkit-scrollbar-thumb:hover{
      background: #ccc;
    }
    .item {
      border-bottom: 1px solid #f5f5f5;
      padding: 10px 0;
      position: relative;
      i {
        position: absolute;
        bottom: 38px;
        right: 0;
        opacity: 0;
        color: #666;
        transition: all .5s;
      }
      &:hover {
        i {
          opacity: 1;
          cursor: pointer;
        }
      }
      a {
        display: flex;
        align-items: center;
        img {
          height: 80px;
          width: 80px;
        }
        .center {
          padding: 0 10px;
          width: 200px;
          .name {
            font-size: 16px;
          }
          .attr {
            color: #999;
            padding-top: 5px;
          }
        }
        .right {
          width: 100px;
          padding-right: 20px;
          text-align: center;
          .price {
            font-size: 16px;
            color: @priceColor;
          }
          .count {
            color: #999;
            margin-top: 5px;
            font-size: 16px;
          }
        }
      }
    }
  }
}
</style>
