<script setup lang="ts">
import { ref, onMounted, onUnmounted } from "vue";
import type { EChartsOption } from "echarts";
import ThreeScene from "./components/ThreeScene.vue";
import ChartCard from "./components/ChartCard.vue";
import ScaleWrapper from "./components/ScaleWrapper.vue";

// 实时客流趋势
const visitorOption = ref<EChartsOption>({
  tooltip: { trigger: "axis" },
  grid: { top: 10, right: 10, bottom: 20, left: 40 },
  xAxis: {
    type: "category",
    data: ["08:00", "09:00", "10:00", "11:00", "12:00", "13:00", "14:00"],
    axisLine: { lineStyle: { color: "#4b5563" } },
    axisLabel: { color: "#9ca3af", fontSize: 10 },
  },
  yAxis: {
    type: "value",
    splitLine: { lineStyle: { color: "#374151" } },
    axisLabel: { color: "#9ca3af", fontSize: 10 },
  },
  series: [
    {
      data: [120, 200, 350, 420, 380, 300, 250],
      type: "line",
      smooth: true,
      symbol: "circle",
      symbolSize: 6,
      lineStyle: { color: "#3b82f6", width: 2 },
      areaStyle: { color: "rgba(59,130,246,0.1)" },
      itemStyle: { color: "#60a5fa" },
    },
  ],
});

// 能耗仪表盘
const energyOption = ref<EChartsOption>({
  series: [
    {
      type: "gauge",
      startAngle: 200,
      endAngle: -20,
      center: ["50%", "55%"],
      radius: "80%",
      detail: {
        formatter: "{value} kW",
        color: "#fff",
        fontSize: 14,
        offsetCenter: [0, "60%"],
      },
      axisLine: {
        lineStyle: {
          width: 15,
          color: [
            [0.3, "#3b82f6"],
            [0.7, "#eab308"],
            [1, "#ef4444"],
          ],
        },
      },
      pointer: { length: "60%", width: 6, itemStyle: { color: "#60a5fa" } },
      axisTick: { distance: -15, length: 5, lineStyle: { color: "#6b7280" } },
      splitLine: { distance: -20, length: 15, lineStyle: { color: "#6b7280" } },
      axisLabel: { color: "#9ca3af", fontSize: 10, distance: 25 },
      data: [{ value: 72, name: "当前能耗" }],
    },
  ],
});

// 安防事件分类
const securityOption = ref<EChartsOption>({
  tooltip: { trigger: "item" },
  legend: {
    bottom: 0,
    textStyle: { color: "#9ca3af", fontSize: 10 },
  },
  series: [
    {
      type: "pie",
      radius: ["45%", "70%"],
      center: ["50%", "45%"],
      avoidLabelOverlap: false,
      itemStyle: {
        borderColor: "#1f2937",
        borderWidth: 2,
      },
      label: { show: false },
      data: [
        { value: 35, name: "越界入侵" },
        { value: 20, name: "异常徘徊" },
        { value: 15, name: "烟雾告警" },
        { value: 10, name: "设备离线" },
      ],
      color: ["#ef4444", "#f59e0b", "#f97316", "#6b7280"],
    },
  ],
});

// 设备状态（柱状图）
const deviceOption = ref<EChartsOption>({
  tooltip: { trigger: "axis" },
  grid: { top: 10, right: 10, bottom: 20, left: 40 },
  xAxis: {
    type: "category",
    data: ["摄像头", "传感器", "闸机", "照明", "空调"],
    axisLine: { lineStyle: { color: "#4b5563" } },
    axisLabel: { color: "#9ca3af", fontSize: 10, rotate: 15 },
  },
  yAxis: {
    type: "value",
    max: 100,
    splitLine: { lineStyle: { color: "#374151" } },
    axisLabel: { color: "#9ca3af", fontSize: 10 },
  },
  series: [
    {
      data: [95, 88, 72, 60, 85],
      type: "bar",
      barWidth: "40%",
      itemStyle: {
        color: "#3b82f6",
        borderRadius: [2, 2, 0, 0],
      },
    },
  ],
});

// 各建筑能耗和安防数据
const buildingDataMap: Record<
  string,
  { energy: number; security: { name: string; value: number }[] }
> = {
  主楼: {
    energy: 72,
    security: [
      { name: "越界入侵", value: 15 },
      { name: "异常徘徊", value: 8 },
      { name: "烟雾告警", value: 5 },
      { name: "设备离线", value: 3 },
    ],
  },
  研发楼: {
    energy: 45,
    security: [
      { name: "越界入侵", value: 5 },
      { name: "异常徘徊", value: 3 },
      { name: "烟雾告警", value: 2 },
      { name: "设备离线", value: 1 },
    ],
  },
  配套楼: {
    energy: 30,
    security: [
      { name: "越界入侵", value: 8 },
      { name: "异常徘徊", value: 6 },
      { name: "烟雾告警", value: 4 },
      { name: "设备离线", value: 5 },
    ],
  },
};

let timer: number;

onMounted(() => {
  handleBuildingClick("主楼");
  timer = window.setInterval(() => {
    // 1. 客流趋势不变（保持随机波动）
    const visitorSeries = visitorOption.value.series as any[];
    if (visitorSeries?.[0]) {
      const data = visitorSeries[0].data as number[];
      for (let i = 0; i < data.length; i++) {
        data[i] = Math.max(0, data[i] + Math.floor(Math.random() * 40 - 20));
      }
      visitorOption.value = { ...visitorOption.value };
    }

    // 2. 能耗波动（在当前值基础上 ±5 内）
    const energySeries = energyOption.value.series as any[];
    if (energySeries?.[0]?.data) {
      let val = energySeries[0].data[0].value as number;
      val = Math.max(
        0,
        Math.min(100, val + Math.floor(Math.random() * 10 - 5)),
      );
      energySeries[0].data[0].value = val;
      energyOption.value = { ...energyOption.value };
    }

    // 3. 安防事件波动（保持类别不变，值随机增减）
    const securitySeries = securityOption.value.series as any[];
    if (securitySeries?.[0]?.data) {
      for (const item of securitySeries[0].data) {
        item.value = Math.max(
          1,
          item.value + Math.floor(Math.random() * 6 - 3),
        );
      }
      securityOption.value = { ...securityOption.value };
    }

    // 4. 设备状态波动
    const deviceSeries = deviceOption.value.series as any[];
    if (deviceSeries?.[0]) {
      const data = deviceSeries[0].data as number[];
      for (let i = 0; i < data.length; i++) {
        data[i] = Math.min(
          100,
          Math.max(60, data[i] + Math.floor(Math.random() * 6 - 3)),
        );
      }
      deviceOption.value = { ...deviceOption.value };
    }
  }, 3000);
});

onUnmounted(() => {
  clearInterval(timer);
});

const handleBuildingClick = (name: string) => {
  const data = buildingDataMap[name];
  if (!data) {
    return
  }

  // 更新能耗仪表盘
  const energySeries = energyOption.value.series as any[];
  if (energySeries?.[0]?.data) {
    energySeries[0].data[0].value = data.energy;
    energyOption.value = { ...energyOption.value };
  }

  // 更新安防饼图
  const securitySeries = securityOption.value.series as any[];
  if (securitySeries?.[0]) {
    securitySeries[0].data = data.security;
    securityOption.value = { ...securityOption.value };
  }
};
</script>

<template>
  <ScaleWrapper>
    <div
      class="w-full h-full bg-gray-900 text-white flex flex-col overflow-hidden"
    >
      <header
        class="h-16 flex items-center justify-center text-2xl font-bold border-b border-gray-700 select-none"
      >
        <span
          class="bg-gradient-to-r from-blue-400 to-cyan-300 bg-clip-text text-transparent"
        >
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
          <ThreeScene @building-click="handleBuildingClick" />
        </section>

        <!-- 右侧图表区 -->
        <aside class="w-1/5 p-4 border-l border-gray-700 space-y-4">
          <div class="h-1/2">
            <ChartCard title="安防事件分类" :option="securityOption" />
          </div>
          <div class="h-1/2">
            <ChartCard title="设备在线率" :option="deviceOption" />
          </div>
        </aside>
      </main>
    </div>
  </ScaleWrapper>
</template>
