<template>
  <div class="goods-comment">
    <div class="head" v-if="commentInfo">
      <div class="data">
        <p><span>{{ commentInfo.salesCount }}</span><span>人购买</span></p>
        <p><span>{{ commentInfo.praisePercent }}</span><span>好评率</span></p>
      </div>
      <div class="tags">
        <div class="dt">大家都在说：</div>
        <div class="dd">
          <a
            href="javascript:;"
            v-for="(item, i) in commentInfo.tags"
            :key="item.title"
            :class="{ active: currentTagIndex === i }"
            @click="changeTag(i)">{{ item.title }}（{{ item.tagCount }}）</a>
        </div>
      </div>
    </div>
    <div class="sort">
      <span>排序：</span>
      <a
        href="javascript:;"
        :class="{ active: reqParams.sortField === null }"
        @click="changeSort(null)">默认</a>
      <a
        href="javascript:;"
        :class="{ active: reqParams.sortField === 'createTime' }"
        @click="changeSort('createTime')">最新</a>
      <a
        href="javascript:;"
        :class="{ active: reqParams.sortField === 'praiseCount' }"
        @click="changeSort('praiseCount')">最热</a>
    </div>
    <!-- 列表 -->
    <div class="list">
      <div class="item" v-for="item in commentList" :key="item.member.id">
        <div class="user">
          <img :src="item.member.avatar" alt="">
          <span>{{ formatNickname(item.member.nickname) }}</span>
        </div>
        <div class="body">
          <div class="score">
            <i v-for="i in item.score" :key="i+'1'" class="iconfont icon-wjx01"></i>
            <i v-for="i in 5 - item.score" :key="i+'2'" class="iconfont icon-wjx02"></i>
            <span class="attr">{{ formatSpecs(item.orderInfo.specs) }}</span>
          </div>
          <div class="text">{{ item.content }}</div>
          <!-- 使用图片组件 -->
          <goods-comment-image v-if="item.pictures.length" :pictures="item.pictures"></goods-comment-image>
          <div class="time">
            <span>{{ item.createTime }}</span>
            <span class="zan"><i class="iconfont icon-dianzan"></i>{{ item.praiseCount }}</span>
          </div>
        </div>
      </div>
    </div>
    <!-- 使用分页组件 -->
    <xtx-pagination @current-change="changePager" :total="total" :current-page="reqParams.page"></xtx-pagination>
  </div>
</template>

<script>
import { ref, reactive, inject, watch } from 'vue'
import { findGoodsComment, findGoodsCommentList } from '@/api/product'
import GoodsCommentImage from './goods-comment-image'

const getCommentInfo = (id) => {
  const commentInfo = ref(null)
  findGoodsComment(id).then(({ result }) => {
    // 添加两条数据
    result.tags.unshift({
      type: 'img',
      title: '图片',
      tagCount: result.hasPictureCount
    })
    result.tags.unshift({
      type: 'all',
      title: '全部评价',
      tagCount: result.evaluateCount
    })
    commentInfo.value = result
  })
  return commentInfo
}
export default {
  name: 'GoodsComment',
  components: {
    GoodsCommentImage
  },
  setup () {
    const goods = inject('goods')
    const commentInfo = getCommentInfo(goods.id)
    const currentTagIndex = ref(0)
    const changeTag = (i) => {
      currentTagIndex.value = i
      // 设置有图和标签条件
      const currTag = commentInfo.value.tags[i]
      if (currTag.type === 'all') {
        reqParams.hasPicture = false
        reqParams.tag = null
      } else if (currTag.type === 'img') {
        reqParams.hasPicture = true
        reqParams.tag = null
      } else {
        reqParams.hasPicture = false
        reqParams.tag = currTag.title
      }
      reqParams.page = 1
    }
    // 筛选条件准备
    const reqParams = reactive({
      page: 1,
      pageSize: 10,
      hasPicture: null,
      tag: null,
      sortField: null
    })
    // 改变排序
    const changeSort = (type) => {
      reqParams.sortField = type
      reqParams.page = 1
    }
    // 初始化或者筛选条件改变后，获取列表数据
    const commentList = ref([])
    // 分页 记录总条数
    const total = ref(0)
    watch(reqParams, async () => {
      const { result } = await findGoodsCommentList(goods.id, reqParams)
      commentList.value = result.items
      // console.log(commentList.value)
      total.value = result.counts
    }, { immediate: true })
    // 定义转换数据的函数（对应vue2.0的过滤器）
    const formatSpecs = (specs) => {
      return specs.reduce((p, c) => {
        return `${p} ${c.name}：${c.nameValue}`
      }, '').trim()
    }
    const formatNickname = (nickname) => {
      return nickname.substr(0, 1) + '*****' + nickname.substr(-1)
    }
    // 改变分页函数
    const changePager = (np) => {
      reqParams.page = np
    }
    return {
      commentInfo,
      currentTagIndex,
      changeTag,
      reqParams,
      changeSort,
      commentList,
      formatSpecs,
      formatNickname,
      total,
      changePager
    }
  }
}
</script>

<style scoped lang="less">
.goods-comment {
  .head {
    display: flex;
    padding: 30px 0;
    .data {
      width: 340px;
      display: flex;
      padding: 20px;
      p {
        flex: 1;
        text-align: center;
        span {
          display: block;
          &:first-child {
            font-size: 32px;
            color: @priceColor;
          }
          &:last-child {
            color: #999;
          }
        }
      }
    }
    .tags {
      flex: 1;
      display: flex;
      border-left: 1px solid #f5f5f5;
      .dt {
        font-weight: bold;
        width: 100px;
        text-align: right;
        line-height: 42px;
      }
      .dd {
        flex: 1;
        display: flex;
        flex-wrap: wrap;
        > a {
          width: 132px;
          height: 42px;
          margin-left: 20px;
          margin-bottom: 20px;
          border-radius: 4px;
          border: 1px solid #e4e4e4;
          background: #f5f5f5;
          color: #999;
          text-align: center;
          line-height: 40px;
          &:hover {
            border-color: @xtxColor;
            background: lighten(@xtxColor,50%);
            color: @xtxColor;
          }
          &.active {
            border-color: @xtxColor;
            background: @xtxColor;
            color: #fff;
          }
        }
      }
    }
  }
  .sort {
    height: 60px;
    line-height: 60px;
    border-top: 1px solid #f5f5f5;
    border-bottom: 1px solid #f5f5f5;
    margin: 0 20px;
    color: #666;
    > span {
      margin-left: 20px;
    }
    > a {
      margin-left: 30px;
      &.active,&:hover {
        color: @xtxColor;
      }
    }
  }
  .list {
    padding: 0 20px;
    .item {
      display: flex;
      padding: 25px 10px;
      border-bottom: 1px solid #f5f5f5;
      .user {
        width: 160px;
        img {
          width: 40px;
          height: 40px;
          border-radius: 50%;
          overflow: hidden;
        }
        span {
          padding-left: 10px;
          color: #666;
        }
      }
      .body {
        flex: 1;
        .score {
          line-height: 40px;
          .iconfont {
            color: #ff9240;
            padding-right: 3px;
          }
          .attr {
            padding-left: 10px;
            color: #666;
          }
        }
      }
      .text {
        color: #666;
        line-height: 24px;
      }
      .time {
        color: #999;
        display: flex;
        justify-content: space-between;
        margin-top: 5px;
      }
    }
  }
}
</style>
