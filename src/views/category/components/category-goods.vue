<template>
  <router-link class="category-goods" :to="`/product/${goods.id}`">
    <img :src="goods.picture" alt="">
    <p class="name ellipsis">{{ goods.name }}</p>
    <p class="desc ellipsis">{{ goods.desc }}</p>
    <p class="price">&yen;{{ goods.price}}</p>
  </router-link>
</template>

<script>
export default {
  name: 'CategoryGoods',
  props: {
    goods: {
      type: Object,
      // 返回对象需要加小括号
      default: () => ({})
    }
  }
}
</script>

<style scoped lang="less">
.category-goods {
  display: block;
  width: 220px;
  padding: 20px 30px;
  text-align: center;
  .hoverShadow();
  img {
    width: 160px;
    height: 160px;
  }
  p {
    padding-top: 10px;
  }
  .name {
    font-size: 16px;
  }
  .desc {
    color: #999;
    height: 29px;
  }
  .price {
    color: @priceColor;
    font-size: 20px;
  }
}
</style>
