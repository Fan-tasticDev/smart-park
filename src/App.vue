<script setup lang="ts">
import { ref } from 'vue'
import type { EChartsOption } from 'echarts'
import ThreeScene from './components/ThreeScene.vue'
import ChartCard from './components/ChartCard.vue'

// 实时客流趋势
const visitorOption = ref<EChartsOption>({
  tooltip: { trigger: 'axis' },
  grid: { top: 10, right: 10, bottom: 20, left: 40 },
  xAxis: {
    type: 'category',
    data: ['08:00', '09:00', '10:00', '11:00', '12:00', '13:00', '14:00'],
    axisLine: { lineStyle: { color: '#4b5563' } },
    axisLabel: { color: '#9ca3af', fontSize: 10 },
  },
  yAxis: {
    type: 'value',
    splitLine: { lineStyle: { color: '#374151' } },
    axisLabel: { color: '#9ca3af', fontSize: 10 },
  },
  series: [
    {
      data: [120, 200, 350, 420, 380, 300, 250],
      type: 'line',
      smooth: true,
      symbol: 'circle',
      symbolSize: 6,
      lineStyle: { color: '#3b82f6', width: 2 },
      areaStyle: { color: 'rgba(59,130,246,0.1)' },
      itemStyle: { color: '#60a5fa' },
    },
  ],
})

// 能耗仪表盘
const energyOption = ref<EChartsOption>({
  series: [
    {
      type: 'gauge',
      startAngle: 200,
      endAngle: -20,
      center: ['50%', '55%'],
      radius: '80%',
      detail: {
        formatter: '{value} kW',
        color: '#fff',
        fontSize: 14,
        offsetCenter: [0, '60%'],
      },
      axisLine: {
        lineStyle: {
          width: 15,
          color: [
            [0.3, '#3b82f6'],
            [0.7, '#eab308'],
            [1, '#ef4444'],
          ],
        },
      },
      pointer: { length: '60%', width: 6, itemStyle: { color: '#60a5fa' } },
      axisTick: { distance: -15, length: 5, lineStyle: { color: '#6b7280' } },
      splitLine: { distance: -20, length: 15, lineStyle: { color: '#6b7280' } },
      axisLabel: { color: '#9ca3af', fontSize: 10, distance: 25 },
      data: [{ value: 72, name: '当前能耗' }],
    },
  ],
})

// 安防事件分类
const securityOption = ref<EChartsOption>({
  tooltip: { trigger: 'item' },
  legend: {
    bottom: 0,
    textStyle: { color: '#9ca3af', fontSize: 10 },
  },
  series: [
    {
      type: 'pie',
      radius: ['45%', '70%'],
      center: ['50%', '45%'],
      avoidLabelOverlap: false,
      itemStyle: {
        borderColor: '#1f2937',
        borderWidth: 2,
      },
      label: { show: false },
      data: [
        { value: 35, name: '越界入侵' },
        { value: 20, name: '异常徘徊' },
        { value: 15, name: '烟雾告警' },
        { value: 10, name: '设备离线' },
      ],
      color: ['#ef4444', '#f59e0b', '#f97316', '#6b7280'],
    },
  ],
})
</script>

<template>
  <div class="w-full h-full bg-gray-900 text-white flex flex-col overflow-hidden">
    <header class="h-16 flex items-center justify-center text-2xl font-bold border-b border-gray-700 select-none">
      <span class="bg-gradient-to-r from-blue-400 to-cyan-300 bg-clip-text text-transparent">
        智慧园区数字孪生大屏
      </span>
    </header>

    <main class="flex-1 flex">
      <!-- 左侧图表区 -->
      <aside class="w-1/5 p-4 border-r border-gray-700 space-y-4">
        <div class="h-1/2">
          <ChartCard title="实时客流趋势" :option="visitorOption" />
        </div>
        <div class="h-1/2">
          <ChartCard title="能耗监测" :option="energyOption" />
        </div>
      </aside>

      <!-- 中间 3D 场景区 -->
      <section class="flex-1 relative">
        <ThreeScene />
      </section>

      <!-- 右侧图表区 -->
      <aside class="w-1/5 p-4 border-l border-gray-700 space-y-4">
        <div class="h-1/2">
          <ChartCard title="安防事件分类" :option="securityOption" />
        </div>
        <div class="h-1/2 bg-gray-800 rounded-xl p-3 flex items-center justify-center text-gray-400 text-sm">
          设备状态（占位）
        </div>
      </aside>
    </main>
  </div>
</template>


