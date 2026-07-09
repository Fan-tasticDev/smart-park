<template>
  <div ref="container" class="w-full h-full" />
</template>

<script setup lang="ts">
import { ref, onMounted, onUnmounted } from 'vue'
import * as THREE from 'three'
import { OrbitControls } from 'three/examples/jsm/controls/OrbitControls.js'

const container = ref<HTMLDivElement>()

let scene: THREE.Scene
let camera: THREE.PerspectiveCamera
let renderer: THREE.WebGLRenderer
let controls: OrbitControls
let animationId: number

onMounted(() => {
  if (!container.value) return

  // 1. 场景
  scene = new THREE.Scene()
  scene.background = new THREE.Color(0x0f172a) // 深蓝灰背景，与大屏协调

  // 2. 相机
  const aspect = container.value.clientWidth / container.value.clientHeight
  camera = new THREE.PerspectiveCamera(45, aspect, 0.1, 1000)
  camera.position.set(8, 6, 8)
  camera.lookAt(0, 0, 0)

  // 3. 渲染器
  renderer = new THREE.WebGLRenderer({ antialias: true, alpha: true })
  renderer.setSize(container.value.clientWidth, container.value.clientHeight)
  renderer.setPixelRatio(Math.min(window.devicePixelRatio, 2)) // 限制过高像素比，优化性能
  renderer.shadowMap.enabled = true // 开启阴影
  container.value.appendChild(renderer.domElement)

  // 4. 光照
  // 环境光提供基础亮度
  const ambientLight = new THREE.AmbientLight(0x404066)
  scene.add(ambientLight)

  // 主光源方向光，产生立体感
  const dirLight = new THREE.DirectionalLight(0xffffff, 1)
  dirLight.position.set(10, 20, 10)
  dirLight.castShadow = true
  dirLight.shadow.mapSize.width = 1024
  dirLight.shadow.mapSize.height = 1024
  scene.add(dirLight)

  // 辅助背光，让暗部不至于太黑
  const backLight = new THREE.DirectionalLight(0x4488ff, 0.5)
  backLight.position.set(-10, 5, -10)
  scene.add(backLight)

  // 5. 控制器
  controls = new OrbitControls(camera, renderer.domElement)
  controls.enableDamping = true
  controls.dampingFactor = 0.05
  controls.maxPolarAngle = Math.PI / 2 // 限制上下旋转角度，保持俯视视角
  controls.target.set(0, 0, 0)
  controls.update()

  // 6. 添加园区占位模型：一个带描边的半透明立方体，代表建筑
  const geometry = new THREE.BoxGeometry(3, 2, 3)
  const material = new THREE.MeshPhongMaterial({
    color: 0x3b82f6,
    transparent: true,
    opacity: 0.8,
    emissive: new THREE.Color(0x0a2a5e),
    shininess: 30,
  })
  const building = new THREE.Mesh(geometry, material)
  building.position.y = 1 // 让底部对齐地面
  building.castShadow = true
  building.receiveShadow = true
  scene.add(building)

  // 添加发光边框
  const edges = new THREE.EdgesGeometry(geometry)
  const lineMat = new THREE.LineBasicMaterial({ color: 0x60a5fa })
  const wireframe = new THREE.LineSegments(edges, lineMat)
  building.add(wireframe)

  // 地面网格，增强空间感
  const gridHelper = new THREE.GridHelper(10, 20, 0x334155, 0x1e293b)
  scene.add(gridHelper)

  // 7. 渲染循环
  const animate = () => {
    animationId = requestAnimationFrame(animate)
    controls.update()
    renderer.render(scene, camera)
  }
  animate()

  // 8. 窗口大小调整
  window.addEventListener('resize', onResize)
})

const onResize = () => {
  if (!container.value) return
  const width = container.value.clientWidth
  const height = container.value.clientHeight
  camera.aspect = width / height
  camera.updateProjectionMatrix()
  renderer.setSize(width, height)
}

onUnmounted(() => {
  cancelAnimationFrame(animationId)
  window.removeEventListener('resize', onResize)
  renderer?.dispose()
  controls?.dispose()
  scene?.clear()
})
</script>