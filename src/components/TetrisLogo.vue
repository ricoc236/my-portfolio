<template>
  <div
    class="tetris-wrapper"
    :class="{ 'is-hovered': isHovered }"
    @mouseenter="triggerClear"
    @mouseleave="resetAnimation"
  >
    <canvas
      ref="canvas"
      :width="internalSize"
      :height="internalSize"
      class="tetris-canvas"
    ></canvas>
  </div>
</template>

<script setup lang="ts">
import { ref, onMounted, onBeforeUnmount } from "vue"



const internalSize = 120
const gridW = 8
const gridH = 8
const cell = internalSize / gridW

const canvas = ref<HTMLCanvasElement | null>(null)
const isHovered = ref(false)


const clearPhase = ref(0)



const grid: (string | null)[][] =
  Array.from({ length: gridH }, () => Array(gridW).fill(null))



const shapes = [
  [[1, 1, 1, 1]],           // I
  [[1, 1], [1, 1]],         // O
  [[0, 1, 0], [1, 1, 1]],   // T
  [[1, 1, 0], [0, 1, 1]],   // S
  [[0, 1, 1], [1, 1, 0]],   // Z
  [[1, 0, 0], [1, 1, 1]],   // J
  [[0, 0, 1], [1, 1, 1]]    // L
]

const colors = [
  "#00f0f0",  // I - cyan
  "#f0f000",  // O - yellow
  "#a000f0",  // T - purple
  "#00f000",  // S - green
  "#f00000",  // Z - red
  "#0000f0",  // J - blue
  "#f0a000"   // L - orange
]



let pieces: any[] = []
let animationId: number | null = null


function buildPresetStack() {

  for (let r = 0; r < gridH; r++) {
    grid[r] = Array(gridW).fill(null);
  }

  for (let r = gridH - 4; r < gridH; r++) {
    const currentRow = grid[r];
    if (!currentRow) continue;
    for (let c = 0; c < gridW; c++) {
      if (c === 0) continue; 
      const colorIndex = Math.floor(Math.random() * colors.length);

      currentRow[c] = colors[colorIndex]!;
    }
  }
}


interface ScriptedPiece {
  
  shape: number[][]
  color: string
  x: number
  y: number       
  targetY: number   
  speed: number
  done: boolean
}

let scriptedPiece: ScriptedPiece | null = null

function createScriptedIPiece(): ScriptedPiece {
  return {
    shape: [[1], [1], [1], [1]],
    color: "#00f0f0",
    x: 0,
    y: -4,            
    targetY: gridH - 4, 
    speed: 0.15,
    done: false
  }
}



function rotateMatrix(matrix: number[][], times: number): number[][] {
  let m: number[][] = matrix && matrix.length > 0 ? matrix : [[1]]
  for (let t = 0; t < times; t++) {
    m = m[0] ? m[0].map((_, i) => m.map(row => row[i]).reverse()) as number[][] : [[1]]
  }
  return m
}

function randomPiece() {
  const i = Math.floor(Math.random() * shapes.length)
  const rot = Math.floor(Math.random() * 4)
  const baseShape = shapes[i] ?? [[1]]
  const shape = rotateMatrix(baseShape, rot)
  return {
    shape,
    color: colors[i],
    x: shape[0] ? Math.floor(Math.random() * (gridW - shape[0].length + 1)) : 0,
    y: -shape.length,
    speed: 0.08 + Math.random() * 0.04
  }
}


function collides(p: any) {
  for (let r = 0; r < p.shape.length; r++)
    for (let c = 0; c < p.shape[r].length; c++) {
      if (!p.shape[r][c]) continue
      const y = Math.floor(p.y + r)
      const x = p.x + c
      if (x < 0 || x >= gridW) return true
      if (y >= gridH) return true
      if (y >= 0 && grid[y]?.[x]) return true
    }
  return false
}


function lockPiece(p: any) {
  for (let r = 0; r < p.shape.length; r++)
    for (let c = 0; c < p.shape[r].length; c++) {
      if (!p.shape[r][c]) continue
      const y = Math.floor(p.y + r)
      const x = p.x + c
      if (y >= 0 && y < gridH && grid[y]) grid[y][x] = p.color
    }
}


function isGameOver() {
  return grid[0]?.some(cell => cell !== null)
}

function getFullRows() {
  return grid
    .map((row, i) => (row.every(v => v !== null) ? i : -1))
    .filter(i => i !== -1)
}

function clearRows() {
  const full = getFullRows()
  full.forEach(r => {
    grid[r] = Array(gridW).fill(null)
  })
}

type HoverState = 'idle' | 'dropping' | 'flashing' | 'done'
let hoverState: HoverState = 'idle'

function update() {
  if (hoverState === 'idle') {
    
    if (clearPhase.value !== 0) return

    if (pieces.length === 0) {
      pieces = [randomPiece()]
      return
    }

    const p = pieces[0]
    p.y += p.speed

    if (collides(p)) {
      p.y -= p.speed
      lockPiece(p)

      if (isGameOver()) {
        clearPhase.value = 1
        setTimeout(() => { resetAnimation() }, 300)
        return
      }

      const full = getFullRows()
      if (full.length) clearRows()

      pieces = [randomPiece()]
    }

  } else if (hoverState === 'dropping' && scriptedPiece) {

    if (!scriptedPiece.done) {
      scriptedPiece.y += scriptedPiece.speed

      if (scriptedPiece.y >= scriptedPiece.targetY) {
        scriptedPiece.y = scriptedPiece.targetY
        scriptedPiece.done = true

    
        lockPiece(scriptedPiece)
        scriptedPiece = null

      
        hoverState = 'flashing'
        clearPhase.value = 1

        setTimeout(() => {
          if (!isHovered.value) return
          clearRows()
          clearPhase.value = 2
          hoverState = 'done'
        }, 600)
      }
    }
  }
}



function draw() {
  const ctx = canvas.value?.getContext("2d")
  if (!ctx) return

  ctx.clearRect(0, 0, internalSize, internalSize)


  ctx.fillStyle = "#6D8196"
  ctx.beginPath()
  ctx.arc(internalSize / 2, internalSize / 2, internalSize / 2, 0, Math.PI * 2)
  ctx.fill()


  ctx.save()
  ctx.beginPath()
  ctx.arc(internalSize / 2, internalSize / 2, internalSize / 2, 0, Math.PI * 2)
  ctx.clip()


  for (let r = 0; r < gridH; r++)
    for (let c = 0; c < gridW; c++) {
      if (!grid[r]?.[c]) continue
      ctx.fillStyle = grid[r]?.[c]!
      ctx.fillRect(c * cell + 1, r * cell + 1, cell - 2, cell - 2)
    }


  if (hoverState === 'idle' && clearPhase.value === 0 && pieces.length > 0) {
    const p = pieces[0]
    ctx.fillStyle = p.color
    p.shape.forEach((row: number[], r: number) =>
      row.forEach((v: number, c: number) => {
        if (!v) return
        ctx.fillRect((p.x + c) * cell + 1, (p.y + r) * cell + 1, cell - 2, cell - 2)
      })
    )
  }


  if (hoverState === 'dropping' && scriptedPiece) {
    ctx.fillStyle = scriptedPiece.color
    scriptedPiece.shape.forEach((row, r) =>
      row.forEach((v, c) => {
        if (!v) return
        ctx.fillRect(
          (scriptedPiece!.x + c) * cell + 1,
          (scriptedPiece!.y + r) * cell + 1,
          cell - 2,
          cell - 2
        )
      })
    )
  }

  if (clearPhase.value === 1) {
    
    const isVisible = Math.floor(Date.now() / 150) % 2 === 0
    const rows = getFullRows()
    
    rows.forEach(r => {
      for (let c = 0; c < gridW; c++) {
        if (isVisible) {
         
          ctx.fillStyle = "rgba(255, 255, 255, 0.9)"
          ctx.fillRect(c * cell, r * cell, cell, cell)
       
          ctx.strokeStyle = "rgba(255, 255, 255, 0.5)"
          ctx.lineWidth = 1
          ctx.strokeRect(c * cell + 2, r * cell + 2, cell - 4, cell - 4)
        } else {
   
          const originalColor = typeof grid[r]?.[c] === "string" ? grid[r][c] as string : "#FFFFFF"
          ctx.fillStyle = originalColor
          ctx.fillRect(c * cell + 1, r * cell + 1, cell - 2, cell - 2)
        }
      }
    })
  }

  ctx.restore()
}



function loop() {
  update()
  draw()
  animationId = requestAnimationFrame(loop)
}



function triggerClear() {
  if (isHovered.value) return  
  isHovered.value = true


  hoverState = 'dropping'
  clearPhase.value = 0
  pieces = []


  buildPresetStack()


  scriptedPiece = createScriptedIPiece()
}



function resetAnimation() {
  isHovered.value = false
  hoverState = 'idle'
  clearPhase.value = 0
  scriptedPiece = null

  for (let r = 0; r < gridH; r++)
    grid[r] = Array(gridW).fill(null)

  pieces = [randomPiece()]
}

onMounted(() => {
  pieces = [randomPiece()]
  loop()
})

onBeforeUnmount(() => {
  if (animationId) cancelAnimationFrame(animationId)
})
</script>

<style scoped>
.tetris-wrapper {
  width: 2.5rem;
  height: 2.5rem;
  cursor: pointer;
  transition: all .5s cubic-bezier(.175, .885, .32, 1.275);
  display: flex;
  align-items: center;
  justify-content: center;
}

.tetris-wrapper.is-hovered {
  width: 4.5rem;
  height: 4.5rem;
  transform: rotate(360deg);
}

.tetris-canvas {
  width: 100%;
  height: 100%;
  border-radius: 9999px;
  box-shadow: 0 4px 12px rgba(0, 0, 0, .15);
}
</style>