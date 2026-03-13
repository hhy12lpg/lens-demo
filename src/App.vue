<template>
  <n-config-provider :theme="darkTheme">
    <n-layout style="min-height: 100vh; padding: 20px;">
      <n-layout-header style="text-align: center; padding-bottom: 20px;">
        <n-h1 style="margin: 0;">凸透镜成像规律演示</n-h1>
        <n-text depth="3">
          拖动左侧物体，动态观察光路与 
          <span v-html="formulaHtml" style="margin: 0 5px;"></span> 
          的变化规律
        </n-text>
      </n-layout-header>

      <n-layout-content>
        <n-grid :x-gap="20" :y-gap="20" cols="1 s:1 m:3" responsive="screen">
          <n-gi :span="2">
            <n-card title="动态光路图" size="large" hoverable style="height: 100%;">
              <div class="svg-container">
                <svg ref="mainSvgRef" viewBox="-320 -200 640 400" width="100%" height="100%" 
                     @mousemove="onDrag" @mouseup="stopDrag" @mouseleave="stopDrag">
                  <defs>
                    <marker id="arrow-ray" viewBox="0 0 10 10" refX="5" refY="5" markerWidth="6" markerHeight="6" orient="auto">
                      <path d="M 0 2 L 8 5 L 0 8 z" fill="#F2C97D" />
                    </marker>
                    <pattern id="grid" width="20" height="20" patternUnits="userSpaceOnUse">
                      <path d="M 20 0 L 0 0 0 20" fill="none" stroke="#333" stroke-width="0.5" />
                    </pattern>
                  </defs>

                  <rect x="-320" y="-200" width="640" height="400" fill="url(#grid)" />

                  <g transform="scale(1, -1)">
                    <line x1="-320" y1="0" x2="320" y2="0" stroke="#888" stroke-width="1" stroke-dasharray="5,5" />
                    <path d="M 0 120 Q 15 0 0 -120 Q -15 0 0 120" fill="rgba(112, 192, 232, 0.2)" stroke="#70C0E8" stroke-width="1.5" />
                    <line x1="0" y1="130" x2="0" y2="-130" stroke="#70C0E8" stroke-width="1" stroke-dasharray="4,4" />

                    <circle :cx="-f" cy="0" r="3" fill="#E88080" />
                    <text :x="-f" y="20" fill="#E88080" font-size="12" text-anchor="middle" transform="scale(1, -1)">F</text>
                    <circle :cx="f" cy="0" r="3" fill="#E88080" />
                    <text :x="f" y="20" fill="#E88080" font-size="12" text-anchor="middle" transform="scale(1, -1)">F</text>
                    
                    <circle :cx="-2*f" cy="0" r="3" fill="#E88080" />
                    <text :x="-2*f" y="20" fill="#E88080" font-size="12" text-anchor="middle" transform="scale(1, -1)">2F</text>
                    <circle :cx="2*f" cy="0" r="3" fill="#E88080" />
                    <text :x="2*f" y="20" fill="#E88080" font-size="12" text-anchor="middle" transform="scale(1, -1)">2F</text>

                    <g style="cursor: ew-resize;" @mousedown="startDrag">
                      <line :x1="-u" y1="0" :x2="-u" :y2="h" stroke="#E88080" stroke-width="5" />
                      <polygon :points="`${-u},${h+8} ${-u-5},${h} ${-u+5},${h}`" fill="#E88080" />
                      <text :x="-u" :y="-(h + 15)" fill="#E88080" font-size="13" font-weight="bold" text-anchor="middle" transform="scale(1, -1)" style="pointer-events: none;">物</text>
                      <rect :x="-u-15" y="0" width="30" :height="h+20" fill="transparent" />
                    </g>

                    <g v-if="u !== f">
                      <line :x1="v" y1="0" :x2="v" :y2="imgH" stroke="#70C0E8" stroke-width="4" :stroke-dasharray="isVirtual ? '4,4' : 'none'" />
                      <polygon :points="`${v},${imgH + (isVirtual ? 8 : -8)} ${v-4},${imgH} ${v+4},${imgH}`" fill="#70C0E8" />
                      <text :x="v" :y="-(imgH + (isVirtual ? 15 : -15))" fill="#70C0E8" font-size="13" font-weight="bold" text-anchor="middle" transform="scale(1, -1)">像</text>

                      <line :x1="-u" :y1="h" x2="0" :y2="h" stroke="#F2C97D" stroke-width="1.5" marker-end="url(#arrow-ray)" />
                      <line x1="0" :y1="h" :x2="isVirtual ? 320 : v" :y2="isVirtual ? h - (h/f)*320 : imgH" stroke="#F2C97D" stroke-width="1.5" />
                      <line v-if="isVirtual" x1="0" :y1="h" :x2="v" :y2="imgH" stroke="#70C0E8" stroke-width="1.5" stroke-dasharray="4,4" />

                      <line :x1="-u" :y1="h" :x2="isVirtual ? 320 : v" :y2="isVirtual ?  - (h/u)*320 : imgH" stroke="#F2C97D" stroke-width="1.5" marker-end="url(#arrow-ray)" />
                      <line v-if="isVirtual" x1="0" y1="0" :x2="v" :y2="imgH" stroke="#70C0E8" stroke-width="1.5" stroke-dasharray="4,4" />
                    </g>
                    <g v-else>
                       <line :x1="-u" :y1="h" x2="0" :y2="h" stroke="#F2C97D" stroke-width="1.5" marker-end="url(#arrow-ray)" />
                       <line x1="0" :y1="h" x2="320" :y2="h - (h/f)*320" stroke="#F2C97D" stroke-width="1.5" />
                       <line :x1="-u" :y1="h" x2="0" y2="0" stroke="#F2C97D" stroke-width="1.5" />
                       <line x1="0" y1="0" x2="320" :y2="-(h/f)*320" stroke="#F2C97D" stroke-width="1.5" marker-end="url(#arrow-ray)" />
                    </g>
                  </g>
                </svg>
              </div>
            </n-card>
          </n-gi>

          <n-gi :span="1">
            <n-space vertical :size="20">
              <n-card title="参数控制" hoverable>
                <n-space vertical>
                  <n-text>物距 $u$ : <n-tag type="error" size="small">{{ u.toFixed(1) }}</n-tag></n-text>
                  <n-slider v-model:value="u" :min="10" :max="260" :step="0.5" />
                  
                  <n-grid :cols="2" style="margin-top: 10px;">
                    <n-gi><n-statistic label="焦距 f" :value="f" /></n-gi>
                    <n-gi><n-statistic label="像距 v" :value="vText" /></n-gi>
                  </n-grid>
                  <n-divider style="margin: 12px 0;" />
                  <n-statistic label="当前成像规律">
                    <n-tag :type="ruleColor" size="large" style="font-size: 16px; font-weight: bold;">
                      {{ ruleText }}
                    </n-tag>
                  </n-statistic>
                </n-space>
              </n-card>

              <n-card title="v-u 关系图像" hoverable>
                <div class="svg-container" style="height: 400px; background-color: transparent;">
                  <svg viewBox="-30 -120 400 300" width="100%" height="100%">
                    <g transform="scale(1, -1) translate(0, 0)">
                      <line x1="0" y1="-100" x2="0" y2="100" stroke="#aaa" stroke-width="1" />
                      <line x1="-20" y1="0" x2="240" y2="0" stroke="#aaa" stroke-width="1" />
                      <text x="230" y="-10" fill="#aaa" font-size="10" transform="scale(1, -1)">u</text>
                      <text x="-15" y="95" fill="#aaa" font-size="10" transform="scale(1, -1)">v</text>

                      <line :x1="f" y1="-100" :x2="f" y2="100" stroke="#555" stroke-dasharray="3,3" />
                      <line x1="-20" :y1="f" x2="300" :y2="f" stroke="#555" stroke-dasharray="3,3" />
                      <text :x="f+5" y="95" fill="#555" font-size="10" transform="scale(1, -1)">u=f</text>
                      <text x="220" :y="-f-5" fill="#555" font-size="10" transform="scale(1, -1)">v=f</text>

                      <circle :cx="2*f" :cy="2*f" r="3" fill="#888" />
                      <text :x="2*f+5" :y="-2*f-5" fill="#888" font-size="10" transform="scale(1, -1)">2f</text>

                      <polyline :points="curveReal" fill="none" stroke="#63e2b7" stroke-width="2" />
                      <polyline :points="curveVirtual" fill="none" stroke="#70C0E8" stroke-width="2" />

                      <g v-if="Math.abs(u - f) > 0.1">
                        <line :x1="u" y1="0" :x2="u" :y2="graphV" stroke="#E88080" stroke-dasharray="2,2" stroke-width="1" />
                        <line x1="0" :y1="graphV" :x2="u" :y2="graphV" stroke="#E88080" stroke-dasharray="2,2" stroke-width="1" />
                        <circle :cx="u" :cy="graphV" r="4" fill="#E88080" />
                      </g>
                    </g>
                  </svg>
                </div>
              </n-card>
            </n-space>
          </n-gi>
        </n-grid>
      </n-layout-content>
    </n-layout>
  </n-config-provider>
</template>

<script setup>
import { ref, computed } from 'vue'
import katex from 'katex'
import 'katex/dist/katex.min.css'
import {
  darkTheme, NConfigProvider, NLayout, NLayoutHeader, NLayoutContent,
  NGrid, NGi, NCard, NSpace, NSlider, NTag, NH1, NText, NDivider, NStatistic
} from 'naive-ui'

// --- KaTeX 公式渲染 ---
const formulaHtml = computed(() => {
  return katex.renderToString('\\frac{1}{u} + \\frac{1}{v} = \\frac{1}{f}', {
    throwOnError: false,
    displayMode: false
  })
})

// --- 物理常量与变量 ---
const f = ref(50)
const u = ref(150)
const h = 40

// --- SVG 拖拽逻辑 (带网格吸附) ---
const mainSvgRef = ref(null)
const isDragging = ref(false)

const startDrag = () => { isDragging.value = true }
const stopDrag = () => { isDragging.value = false }

const onDrag = (e) => {
  if (!isDragging.value || !mainSvgRef.value) return
  const pt = mainSvgRef.value.createSVGPoint()
  pt.x = e.clientX
  pt.y = e.clientY
  const svgP = pt.matrixTransform(mainSvgRef.value.getScreenCTM().inverse())
  
  let newU = -svgP.x
  // 强制吸附到 0.5 的网格，彻底解决浮点数精度导致的逻辑越界问题
  newU = Math.round(newU * 2) / 2
  
  if (newU < 10) newU = 10
  if (newU > 260) newU = 260
  
  u.value = newU
}

// --- 核心物理计算 ---
const v = computed(() => {
  if (Math.abs(u.value - f.value) <= 0.1) return Infinity
  return (u.value * f.value) / (u.value - f.value)
})

const imgH = computed(() => {
  if (v.value === Infinity) return 0
  return -h * (v.value / u.value)
})

const isVirtual = computed(() => v.value < 0)

const vText = computed(() => {
  if (v.value === Infinity) return '∞'
  return v.value.toFixed(1)
})

const graphV = computed(() => {
  const maxV = 200, minV = -100
  if (v.value === Infinity) return maxV
  if (v.value > maxV) return maxV
  if (v.value < minV) return minV
  return v.value
})

// --- 严谨的成像规律判断 (已解决重叠与盲区 Bug) ---
const ruleText = computed(() => {
  const eps = 0.1 // 容差值
  
  // 1. 优先判断处于关键点（等式）的情形
  if (Math.abs(u.value - f.value) <= eps) {
    return '不成像 (平行光射出)'
  }
  if (Math.abs(u.value - 2 * f.value) <= eps) {
    return '倒立、等大实像'
  }
  
  // 2. 使用带有容差的严格互斥区间判断
  if (u.value > 2 * f.value + eps) {
    return '倒立、缩小实像'
  } else if (u.value > f.value + eps && u.value < 2 * f.value - eps) {
    return '倒立、放大实像'
  } else if (u.value < f.value - eps) {
    return '正立、放大虚像'
  }
  
  return '未知状态'
})

const ruleColor = computed(() => {
  const eps = 0.1
  if (Math.abs(u.value - f.value) <= eps) return 'warning'
  if (isVirtual.value) return 'info'
  if (Math.abs(u.value - 2 * f.value) <= eps) return 'error' // 2f关键点给个醒目的红色
  return 'success'
})

// --- 函数图像曲线绘制 ---
const curveReal = computed(() => {
  let pts = []
  for (let x = f.value + 1; x <= 260; x += 2) {
    let y = (x * f.value) / (x - f.value)
    if (y < 200) pts.push(`${x},${y}`)
  }
  return pts.join(' ')
})

const curveVirtual = computed(() => {
  let pts = []
  for (let x = 1; x <= f.value - 1; x += 2) {
    let y = (x * f.value) / (x - f.value)
    if (y > -100) pts.push(`${x},${y}`)
  }
  return pts.join(' ')
})
</script>

<style scoped>
.svg-container {
  width: 100%;
  background-color: #18181c;
  border-radius: 8px;
  overflow: hidden;
  display: flex;
  justify-content: center;
  align-items: center;
  position: relative;
}
svg {
  display: block;
}
</style>