<template>
  <div
    class="w-full h-full bg-gray-900 text-white flex flex-col overflow-hidden"
  >
    <!-- 头部标题 -->
    <header
      class="h-16 flex items-center justify-center text-2xl font-bold border-b border-gray-700 select-none"
    >
      <span
        class="bg-gradient-to-r from-blue-400 to-cyan-300 bg-clip-text text-transparent"
      >
        智慧园区数字孪生大屏
      </span>
    </header>

    <!-- 主体三列 -->
    <main class="flex-1 flex">
      <!-- 左侧图表区 -->
      <aside class="w-1/5 p-4 border-r border-gray-700 space-y-4">
        <div
          class="h-1/2 bg-gray-800 rounded-xl p-3 flex items-center justify-center text-gray-400 text-sm"
        >
          实时客流趋势（占位）
        </div>
        <div
          class="h-1/2 bg-gray-800 rounded-xl p-3 flex items-center justify-center text-gray-400 text-sm"
        >
          能耗监测（占位）
        </div>
      </aside>

      <!-- 中间 3D 场景区 -->
      <section class="flex-1 relative">
        <ThreeScene />
      </section>

      <!-- 右侧图表区 -->
      <aside class="w-1/5 p-4 border-l border-gray-700 space-y-4">
        <div
          class="h-1/2 bg-gray-800 rounded-xl p-3 flex items-center justify-center text-gray-400 text-sm"
        >
          安防事件分类（占位）
        </div>
        <div
          class="h-1/2 bg-gray-800 rounded-xl p-3 flex items-center justify-center text-gray-400 text-sm"
        >
          设备状态（占位）
        </div>
      </aside>
    </main>
  </div>
</template>

<script setup lang="ts">
import ThreeScene from "./components/ThreeScene.vue";
</script>
