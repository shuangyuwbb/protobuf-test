<template>
  <div id="custom-progress" class="custom-progress" :style="{width: diameter+'px', height:diameter+'px'}">
    <el-progress
      :show-text="false" :width="diameter" type="circle"
      :percentage="percentage" :stroke-width="stokeWidth"
      stroke-linecap="square"
      stroke="#000"
      style="--el-fill-color-light: '#2d2e30'"
    />
    <svg width="100%" height="100%">
      <defs>
        //x,y即渐变色方向及范围的控制
        <linearGradient
          id="custom" x1="1" y1="0"
          x2="0.3" y2="1"
        >
          <stop
            offset="0%" style="stop-color: #3fedcd" 渐变色颜色
            stop-opacity="0.1" 透明度
          />

          <stop
            offset="100%" style="stop-color: #10e945" 渐变色颜色
            stop-opacity="1" 透明度
          />
        </linearGradient>
      </defs>
    </svg>
    <!-- 外层部分 -->
    <span
      v-for="(item, index) of spans"
      :key="index"
      class="custom-progress-span"
      :style="item"
    />

    <!-- 中间部分 -->
    <div class="custom-progress-center">
      00:00:02
    </div>
  </div>
</template>
<script setup lang="ts">
import { computed, onMounted, ref } from 'vue'
import { interpolateRgb } from 'd3-interpolate'

const props = defineProps({
  diameter: {
    type: Number,
    default: 200
  },
  percentage: {
    type: Number,
    default: 90
  }
})

const stokeWidth = computed(() => {
  return props.diameter / 10
})

enum Color{
  DefaultSpan= '#4b4d51',
  DefaultStoke = '#2d2e30',
  startColor = '#3fedcd',
  EndColor='#10e945'
}

const spans = ref<any>([])
function Circle() {
  const box = document.querySelector('#custom-progress')!

  let miW = box.clientWidth / 2

  let miH = box.clientHeight / 2

  const rects = spanNumber()

  const tempArr = []

  const r = props.diameter / 2 + props.diameter * 0.03

  for (let i = 0; i < rects; i++) {
    let deg = (360 / rects) * i

    let x = r * Math.sin(deg * Math.PI / 180) + miW - props.diameter * 0.015

    let y = miH - r * Math.cos(deg * Math.PI / 180) - props.diameter * 0.01

    const interpolatedColor = i > props.percentage / 100 * rects ? Color.DefaultSpan : interpolateRgb(Color.startColor, Color.EndColor)(i)
    // #4b4d51

    const temp = {
      left: x + 'px',
      top: y + 'px',
      transform: 'rotate(' + (-90 + deg) + 'deg)',
      background: interpolatedColor
    }
    tempArr.push(temp)
  }

  spans.value = tempArr
}

/** 计算周长 */
function perimeter() {
  return Math.PI * props.diameter
}

/** 计算方块个数*/
function spanNumber() {
  return perimeter() / (props.diameter * 0.05)
}

onMounted(() => {
  Circle()
  console.log(spans.value)
})
</script>
<style scoped lang="less">
.custom-progress {
  position: relative;

  :deep(svg > path:nth-child(2)) {
    stroke: url(#custom);
  }

  &-center{
    position: absolute;
    top: 10%;
    left: 10%;
    width: 80%;
    height: 80%;
    background: #33363f;
    border-radius: 50%;
    display: flex;
    justify-content: center;
    align-items: center;
    color: #d5d5d5;
  }

  &-span{
    position: absolute;
    top: 0;
    width: 3%;
    height: 3%;
    display: block;
    background: #10e945;
  }
}
</style>
