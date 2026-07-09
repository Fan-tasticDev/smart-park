<template>
  <div ref="container" class="w-full h-full" />
</template>

<script setup lang="ts">
import { ref, onMounted, onUnmounted } from 'vue'
import * as THREE from 'three'
import { OrbitControls } from 'three/examples/jsm/controls/OrbitControls.js'

const emit = defineEmits<{
  buildingClick: [name: string]
}>()

const container = ref<HTMLDivElement>()

let scene: THREE.Scene
let camera: THREE.PerspectiveCamera
let renderer: THREE.WebGLRenderer
let controls: OrbitControls
let animationId: number
let buildings: THREE.Mesh[] = []
let selectedBuilding: THREE.Mesh | null = null

onMounted(() => {
  if (!container.value) return

  // 1. 场景
  scene = new THREE.Scene()
  scene.background = new THREE.Color(0x0f172a)

  // 2. 相机
  const aspect = container.value.clientWidth / container.value.clientHeight
  camera = new THREE.PerspectiveCamera(45, aspect, 0.1, 1000)
  camera.position.set(12, 8, 12)
  camera.lookAt(0, 1, 0)

  // 3. 渲染器
  renderer = new THREE.WebGLRenderer({ antialias: true, alpha: true })
  renderer.setSize(container.value.clientWidth, container.value.clientHeight)
  renderer.setPixelRatio(Math.min(window.devicePixelRatio, 2))
  renderer.shadowMap.enabled = true
  container.value.appendChild(renderer.domElement)

  // 4. 光照
  const ambientLight = new THREE.AmbientLight(0x404066)
  scene.add(ambientLight)
  const dirLight = new THREE.DirectionalLight(0xffffff, 1)
  dirLight.position.set(10, 20, 10)
  dirLight.castShadow = true
  scene.add(dirLight)
  const backLight = new THREE.DirectionalLight(0x4488ff, 0.4)
  backLight.position.set(-10, 5, -10)
  scene.add(backLight)

  // 5. 控制器
  controls = new OrbitControls(camera, renderer.domElement)
  controls.enableDamping = true
  controls.dampingFactor = 0.05
  controls.maxPolarAngle = Math.PI / 2.2
  controls.target.set(0, 1.5, 0)
  controls.update()

  // 6. 地面网格
  const gridHelper = new THREE.GridHelper(12, 20, 0x334155, 0x1e293b)
  scene.add(gridHelper)

  // 7. 创建多栋建筑
  const buildingData = [
    { name: '主楼', pos: [0, 0, 0], size: [2.5, 3, 2.5], color: 0x3b82f6 },
    { name: '研发楼', pos: [-4.5, 0, -1.5], size: [2, 2.5, 2], color: 0x10b981 },
    { name: '配套楼', pos: [4, 0, 2], size: [1.8, 1.8, 1.8], color: 0xf59e0b },
  ]

  buildingData.forEach(({ name, pos, size, color }) => {
    const geometry = new THREE.BoxGeometry(size[0], size[1], size[2])
    const material = new THREE.MeshPhongMaterial({
      color,
      transparent: true,
      opacity: 0.85,
      emissive: new THREE.Color(color).multiplyScalar(0.3),
      shininess: 20,
    })
    const mesh = new THREE.Mesh(geometry, material)
    mesh.position.set(pos[0], pos[1] + size[1] / 2, pos[2])
    mesh.castShadow = true
    mesh.receiveShadow = true
    mesh.name = name

    // 添加描边
    const edges = new THREE.EdgesGeometry(geometry)
    const lineMat = new THREE.LineBasicMaterial({ color: 0x94a3b8 })
    const wireframe = new THREE.LineSegments(edges, lineMat)
    mesh.add(wireframe)

    scene.add(mesh)
    buildings.push(mesh)
  })

  // 8. 点击交互（射线检测）
  const raycaster = new THREE.Raycaster()
  const mouse = new THREE.Vector2()

const onClick = (event: MouseEvent) => {
  if (!container.value) return
  const rect = container.value.getBoundingClientRect()
  mouse.x = ((event.clientX - rect.left) / rect.width) * 2 - 1
  mouse.y = -((event.clientY - rect.top) / rect.height) * 2 + 1

  raycaster.setFromCamera(mouse, camera)
  // 递归检测子对象，这样可以点到建筑的描边也能识别
  const intersects = raycaster.intersectObjects(buildings, true)

  if (intersects.length > 0) {
    // 向上查找，直到找到属于 buildings 的 Mesh
    let target: THREE.Object3D | null = intersects[0].object
    while (target && !buildings.includes(target as THREE.Mesh)) {
      target = target.parent
    }
    if (!target) return

    const object = target as THREE.Mesh

    // 取消上一个高亮
    if (selectedBuilding && selectedBuilding !== object) {
      const prevEdges = selectedBuilding.children[0] as THREE.LineSegments
      if (prevEdges) (prevEdges.material as THREE.LineBasicMaterial).color.set(0x94a3b8)
    }
    // 设置新高亮
    const edges = object.children[0] as THREE.LineSegments
    if (edges) (edges.material as THREE.LineBasicMaterial).color.set(0xfacc15) // 亮黄色高亮
    selectedBuilding = object

    emit('buildingClick', object.name)
  }
}

  container.value.addEventListener('click', onClick)

  // 9. 渲染循环
  const animate = () => {
    animationId = requestAnimationFrame(animate)
    controls.update()
    renderer.render(scene, camera)
  }
  animate()

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