<template>
  <div class="app">
    <div class="fixed">
      <div class="title">awesome-echarts-theme 🪄</div>
      <div class="switch">
        <el-button-group>
          <el-button
            v-for="item in themeList"
            :key="item.name"
            @click="changeTheme(item.name)"
          >
            {{ item.name }}
          </el-button>
        </el-button-group>
      </div>
    </div>
    <!-- 主题切换按钮 -->

    <div class="chartList">
      <div class="user-echart" v-for="(item, i) in options" :key="i">
        <Echart :option="item" />
      </div>
    </div>
  </div>
</template>

<script lang="ts" setup>
import { themeList, getOptions } from './config'
import Vue from 'vue'
import { reactive, toRefs } from 'vue'
const options = getOptions()

function changeTheme(theme) {
  Vue.prototype.$eventBus.$emit('echarts-theme', theme)
}
console.log(getOptions())
</script>

<style lang="scss" scoped>
.app {
  width: min(1800px, 100%);
  height: 100%;
  margin: 0 auto;
}
.title {
  line-height: 1.5;
  font-size: 60px;

  text-align: center;
  margin: 10px 0;
}
.fixed {
  position: sticky;
  backdrop-filter: saturate(50%) blur(8px);
  top: 0;
  border-radius: 33%;
  z-index: 1;
}
.switch {
  text-align: center;
  margin: 10px 0;
}
.user-echart {
  height: 350px;
}
.chartList {
  display: grid;
  height: 100%;
  gap: 20px;

  grid-template-columns: repeat(auto-fill, minmax(500px, 1fr));
}
</style>
