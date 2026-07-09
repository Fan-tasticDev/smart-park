<template>
  <div ref="wrapper" class="w-screen h-screen bg-black overflow-hidden">
    <div ref="content" class="origin-top-left" :style="{ width: '1920px', height: '1080px' }">
      <slot />
    </div>
  </div>
</template>

<script setup lang="ts">
import { ref, onMounted, onUnmounted } from 'vue'

const wrapper = ref<HTMLDivElement>()
const content = ref<HTMLDivElement>()

const resize = () => {
  if (!wrapper.value || !content.value) return
  const scaleX = wrapper.value.clientWidth / 1920
  const scaleY = wrapper.value.clientHeight / 1080
  const scale = Math.min(scaleX, scaleY)
  content.value.style.transform = `scale(${scale})`
  // 水平居中
  const offsetX = (wrapper.value.clientWidth - 1920 * scale) / 2
  content.value.style.marginLeft = `${offsetX}px`
}

onMounted(() => {
  resize()
  window.addEventListener('resize', resize)
})

onUnmounted(() => {
  window.removeEventListener('resize', resize)
})
</script>