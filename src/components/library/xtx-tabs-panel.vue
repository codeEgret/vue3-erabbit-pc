<template>
  <!-- 装载是内容 -->
  <div class="xtx-tabs-panel" v-show="activeName === name">
    <slot />
  </div>
</template>

<script>
import { inject } from 'vue'

export default {
  name: 'XtxTabsPanel',
  props: {
    label: {
      type: String,
      default: ''
    },
    name: {
      type: String,
      default: ''
    }
  },
  setup () {
    // 当前组件该不该显示，取决于xtx-tabs组件的activeName数据是否和props.name一样
    const activeName = inject('activeName')
    return { activeName }
  }
}
</script>

<style scoped lang="less">

</style>
