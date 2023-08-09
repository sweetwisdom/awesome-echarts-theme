<template>
  <div
    :id="id"
    :class="className"
    :style="{ height: height, width: width }"
    @click="$emit('click')"
  />
</template>

<script>
import { throttle } from 'throttle-debounce'

import darkTheme from './theme/dark.json'
import aliTheme from './theme/alibaba.json'
import defaultTheme from './theme/default.json'
import wechatTheme from './theme/wechat.json'

export default {
  name: 'echart',
  props: {
    className: {
      type: String,
      default: 'chart',
    },
    id: {
      type: String,
      default: 'chart',
    },
    width: {
      type: String,
      default: '100%',
    },
    height: {
      type: String,
      default: '100%',
    },
    option: {
      type: Object,
      default: () => ({}),
    },
  },
  data() {
    return {
      chart: null,
      xData: null,
    }
  },
  watch: {
    option: {
      handler(option) {
        // 设置true清空echart缓存

        option && this.chart.setOption(option, true)
      },
      deep: true,
    },
  },
  mounted() {
    this.initChart()

    this.$eventBus.$on('echarts-theme', (theme) => {
      this.changeTheme(theme)
    })
    window.addEventListener('resize', throttle(1000, this.handleWindowResize))
    // 监听 echarts-theme事件 实现主题切换
    //   window.dispatchEvent(new Event('echarts-theme', { detail: theme }))

    // echarts 点击事件跳转逻辑
    let that = this
    this.chart.on('click', (params) => {
      let data = params.data
      this.$emit('skip', data)
      this.$emit('jumpEvent', params)
      that.$emit('mapClick', params)
    })
  },
  created() {
    this.initTheme()
  },
  beforeDestroy() {
    this.chart.dispose()
    this.chart = null
  },
  methods: {
    initChart() {
      // 初始化echart
      if (!this.option) return
      this.chart = echarts.init(this.$el, 'default')
      this.chart.setOption(this.option, true)
      this.triggerEvent() // 初始化时触发的图表行为
    },
    initTheme() {
      if (window.hasInitTheme) return
      echarts.registerTheme('dark', darkTheme)
      echarts.registerTheme('alibaba', aliTheme)
      echarts.registerTheme('default', defaultTheme)
      echarts.registerTheme('wechat', wechatTheme)
      window.EventTarget = new EventTarget()

      window.hasInitTheme = true
    },
    // 监听 echarts-theme事件 实现主题切换
    changeTheme(theme) {
      this.chart.dispose() // 销毁echart实例
      this.chart = echarts.init(this.$el, theme)
      this.chart.setOption(this.option, true)
    },
    handleWindowResize() {
      if (!this.chart) return
      this.chart.resize()
    },
    triggerEvent() {
      this.$emit('triggerEvent', this.chart) // this.chart传给父组件，图表行为在父组件根据需求编写
    },
  },
  beforeDestroy() {
    window.removeEventListener('resize', this.handleWindowResize)
    window.removeEventListener('echarts-theme', this.changeTheme)
  },
}
</script>

<style lang="scss">
#cursor :hover {
  cursor: default !important;
}
</style>
