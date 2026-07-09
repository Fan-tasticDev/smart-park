# 智慧园区数字孪生大屏

基于 Vue 3 + Three.js + ECharts 构建的数字孪生可视化大屏，支持 3D 园区模型交互、实时数据看板、多分辨率适配。

## 功能
- **3D 数字孪生**：Three.js 渲染园区建筑模型，支持旋转/缩放，点击建筑联动图表
- **数据可视化**：ECharts 实现客流趋势、能耗仪表、安防事件、设备状态
- **实时刷新**：模拟后端数据推送，图表动态更新
- **自适应缩放**：等比缩放适配 4K 至普清屏幕
- **暗色科技风**：专为大屏设计的深色主题

## 技术栈
- Vue 3 + TypeScript + Vite
- Three.js (3D 渲染)
- ECharts + vue-echarts (图表)
- Tailwind CSS v4 (样式)
- Pinia (状态管理，待扩展)

## 本地运行
1. 克隆仓库
2. 安装依赖：`npm install`
3. 运行：`npm run dev`
4. 访问 http://localhost:5173

## 部署
构建：`npm run build`  
预览：`npm run preview`  
可直接部署到 Vercel / Netlify

## 在线演示
[Vercel 部署链接]（稍后补上）