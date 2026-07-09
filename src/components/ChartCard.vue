<template>
  <div class="bg-gray-800 rounded-xl p-4 flex flex-col h-full">
    <h3 class="text-sm text-gray-400 mb-2 font-medium">{{ title }}</h3>
    <div class="flex-1">
      <v-chart :option="option" autoresize />
    </div>
  </div>
</template>

<script setup lang="ts">
import VChart from 'vue-echarts'
import 'echarts'

defineProps<{
  title: string
  option: Record<string, any>  // 放宽类型，避免深层定义冲突
}>()
</script>