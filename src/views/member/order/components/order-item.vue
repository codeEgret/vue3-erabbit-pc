<template>
  <div class="order-item">
    <div class="head">
      <span>下单时间：{{ order.createTime }}</span>
      <span>订单编号：{{ order.id }}</span>
      <!-- 未付款，倒计时时间还有 -->
      <span class="down-time" v-if="order.orderState === 1">
        <i class="iconfont icon-down-time"></i>
        <b>付款截止：{{ timeText }}</b>
      </span>
      <!-- 已完成 已取消 -->
      <a
        v-if="[5,6].includes(order.orderState)"
        href="javascript:;"
        class="del"
        @click="$emit('on-delete')"
      >
        删除
      </a>
    </div>
    <div class="body">
      <div class="column goods">
        <ul>
          <li v-for="goods in order.skus" :key="goods.id">
            <router-link class="image" :to="`/product/${goods.spuId}`">
              <img :src="goods.image" alt="" />
            </router-link>
            <div class="info">
              <p class="name ellipsis-2">{{ goods.name }}</p>
              <p class="attr ellipsis">{{ goods.attrsText }}</p>
            </div>
            <div class="price">¥{{ goods.realPay }}</div>
            <div class="count">x{{ goods.quantity }}</div>
          </li>
        </ul>
      </div>
      <div class="column state">
        <p>{{ orderStatus[order.orderState].label }}</p>
        <!-- 待收货：查看物流 -->
        <!-- 待评价：评价商品 -->
        <!-- 已完成：查看评价 -->
        <p v-if="order.orderState === 3" @click="$emit('on-logistics')"><a href="javascript:;" class="green">查看物流</a></p>
        <p v-if="order.orderState === 4"><a href="javascript:;" class="green">评价商品</a></p>
        <p v-if="order.orderState === 5"><a href="javascript:;" class="green">查看评价</a></p>
      </div>
      <div class="column amount">
        <p class="red">¥{{ order.payMoney }}</p>
        <p>（含运费：¥{{ order.postFee }}）</p>
        <p>在线支付</p>
      </div>
      <div class="column action">
        <!-- 待支付：立即付款，查看详情，取消订单 -->
        <!-- 待发货：查看详情，再次购买 -->
        <!-- 待收货：确认收货，查看详情，再次购买 -->
        <!-- 待评价：查看详情，再次购买，申请售后 -->
        <!-- 已完成：查看详情，再次购买，申请售后 -->
        <!-- 已取消：查看详情 -->
        <xtx-button
          v-if="order.orderState === 1"
          type="primary"
          size="small"
          @click="$router.push('/member/pay?id='+order.id)"
        >
          立即付款
        </xtx-button>
        <xtx-button
          v-if="order.orderState === 3"
          type="primary"
          size="small"
          @click="$emit('on-confirm')"
        >
          确认收货
        </xtx-button>
        <p>
          <a href="javascript:;" @click="$router.push(`/member/order/${order.id}`)">查看详情</a>
        </p>
        <p v-if="order.orderState === 1" @click="$emit('on-cancel')">
          <a href="javascript:;">取消订单</a>
        </p>
        <p v-if="[2,3,4,5].includes(order.orderState)">
          <a href="javascript:;" @click="$router.push(`/member/checkout?orderId=${order.id}`)">再次购买</a>
        </p>
        <p v-if="[4,5].includes(order.orderState)">
          <a href="javascript:;">申请售后</a>
        </p>
      </div>
    </div>
  </div>
</template>

<script>
import { orderStatus } from '@/api/constant/constant'
import { usePayTime } from '@/hooks'

export default {
  name: 'OrderItem',
  props: {
    order: {
      type: Object,
      default: () => ({})
    }
  },
  emits: ['on-cancel', 'on-delete', 'on-confirm', 'on-logistics'],
  setup (props) {
    const { start, timeText } = usePayTime()
    if (props.order.countdown > -1) {
      start(props.order.countdown)
    } else {
      timeText.value = '已超时'
    }
    return { orderStatus, timeText }
  }
}
</script>

<style scoped lang="less">
.order-item {
  margin-bottom: 20px;
  border: 1px solid #f5f5f5;
  .head {
    height: 50px;
    line-height: 50px;
    background: #f5f5f5;
    padding: 0 20px;
    overflow: hidden;
    span {
      margin-right: 20px;
      &.down-time {
        margin-right: 0;
        float: right;
        i {
          vertical-align: middle;
          margin-right: 3px;
        }
        b {
          vertical-align: middle;
          font-weight: normal;
        }
      }
    }
    .del {
      margin-right: 0;
      float: right;
      color: #999;
    }
  }
  .body {
    display: flex;
    align-items: stretch;
    .column {
      border-left: 1px solid #f5f5f5;
      text-align: center;
      padding: 20px;
      > p {
        padding-top: 10px;
      }
      &:first-child {
        border-left: none;
      }
      &.goods {
        flex: 1;
        padding: 0;
        align-self: center;
        ul {
          li {
            border-bottom: 1px solid #f5f5f5;
            padding: 10px;
            display: flex;
            &:last-child {
              border-bottom: none;
            }
            .image {
              width: 70px;
              height: 70px;
              border: 1px solid #f5f5f5;
            }
            .info {
              width: 220px;
              text-align: left;
              padding: 0 10px;
              p {
                margin-bottom: 5px;
                &.name {
                  height: 38px;
                }
                &.attr {
                  color: #999;
                  font-size: 12px;
                  span {
                    margin-right: 5px;
                  }
                }
              }
            }
            .price {
              width: 100px;
            }
            .count {
              width: 80px;
            }
          }
        }
      }
      &.state {
        width: 120px;
        .green {
          color: @xtxColor;
        }
      }
      &.amount {
        width: 200px;
        .red {
          color: @priceColor;
        }
      }
      &.action {
        width: 140px;
        a {
          display: block;
          &:hover {
            color: @xtxColor;
          }
        }
      }
    }
  }
}
</style>
