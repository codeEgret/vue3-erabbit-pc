<template>
<div class="home-special">
  <home-panel title="最新专题">
    <template #right>
      <xtx-more></xtx-more>
    </template>
    <div class="special-list" ref="target">
      <div class="special-item" v-for="data in result" :key="data.id">
        <router-link to="/">
          <img v-lazyload="data.detailsUrl" alt />
          <div class="meta">
            <p class="title">
              <span class="top ellipsis">{{ data.title }}</span>
              <span class="sub ellipsis">{{ data.summary }}</span>
            </p>
            <span class="price">&yen;{{ data.lowestPrice }}起</span>
          </div>
        </router-link>
        <div class="foot">
          <span class="like"><i class="iconfont icon-hart1"></i>{{ data.collectNum }}</span>
          <span class="view"><i class="iconfont icon-see"></i>{{ data.viewNum }}</span>
          <span class="reply"><i class="iconfont icon-message"></i>{{ data.replyNum }}</span>
        </div>
      </div>
    </div>
  </home-panel>
</div>
</template>

<script>
import { ref } from 'vue'
import { useLazyData } from '@/hooks'
import { findSpecial } from '@/api/home'
import HomePanel from './home-panel'
export default {
  name: 'HomeSpecial',
  components: {
    HomePanel
  },
  setup () {
    const target = ref(null)
    const result = useLazyData(target, findSpecial)
    // console.log(result)
    return { target, result }
  }
}
</script>

<style scoped lang="less">
.home-panel {
  background: #f5f5f5;
}
.special-list {
  height: 380px;
  padding-bottom: 20px;
  display: flex;
  justify-content: space-between;
  .special-item {
    width: 404px;
    background: #fff;
    .hoverShadow();
    a {
      display: block;
      width: 100%;
      height: 288px;
      position: relative;
      img {
        width: 100%;
        height: 100%;
      }
      .meta {
        background-image: linear-gradient(to top,rgba(0, 0, 0, 0.8),transparent 50%);
        position: absolute;
        left: 0;
        top: 0;
        width: 100%;
        height: 288px;
        .title {
          position: absolute;
          bottom: 0px;
          left: 0;
          padding-left: 16px;
          width: 70%;
          height: 70px;
          .top {
            color: #fff;
            font-size: 22px;
            display: block;
          }
          .sub {
            display: block;
            font-size: 19px;
            color: #999;
          }
        }
        .price {
          position: absolute;
          bottom: 25px;
          right: 16px;
          line-height: 1;
          padding: 4px 8px 4px 7px;
          color: @priceColor;
          font-size: 17px;
          background-color: #fff;
          border-radius: 2px;
        }
      }
    }
    .foot {
      height: 72px;
      line-height: 72px;
      padding: 0 20px;
      font-size: 16px;

      i {
        display: inline-block;
        width: 15px;
        height: 14px;
        margin-right: 5px;
        color: #999;
      }
      .like,
      .view {
        float: left;
        margin-right: 25px;
        vertical-align: middle;
      }
      .reply {
        float: right;
        vertical-align: middle;
      }
    }
  }
}
</style>
