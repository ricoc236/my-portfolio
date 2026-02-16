<template>
  <nav class="w-full sticky top-0 z-50 backdrop-blur-md border-b border-white/10" style="background-color: #6D8196ee">
    <div class="max-w-6xl mx-auto px-4 sm:px-6 py-4 flex items-center justify-between">
      
      <div class="flex items-center">
        <TetrisLogo />
      </div>

      <div class="flex-1 hidden md:flex justify-center">
        <ul class="flex items-center gap-10">
          <li><a href="#about" class="nav-link">About</a></li>
          <li><a href="#projects" class="nav-link">Projects</a></li>
          <li><a href="#contact" class="nav-link">Contact</a></li>
        </ul>
      </div>

      <div class="flex items-center gap-2 sm:gap-4">
        
        <div class="hidden sm:block h-8 w-[1px] bg-white/20 mr-2"></div>

        <div class="flex items-center gap-2 sm:gap-3">
          <div class="relative flex items-center justify-center w-9 h-9 sm:w-10 sm:h-10 bg-white/10 rounded-xl border border-white/10 shadow-sm">
            <span v-if="!isNight" class="text-lg sm:text-xl animate-sun-glow">☀️</span>
            <div v-else class="relative text-lg sm:text-xl">
              🌙
              <span class="zzz z1">z</span>
              <span class="zzz z2">z</span>
            </div>
          </div>

          <div class="flex flex-col justify-center text-left leading-tight">
            <div class="flex items-center gap-1.5">
              <span class="w-2 h-2 rounded-full shadow-[0_0_8px_rgba(0,0,0,0.2)]" 
                    :class="isNight ? 'bg-orange-400' : 'bg-green-400'"></span>
              <span class="text-white font-bold text-[11px] sm:text-sm tracking-tight whitespace-nowrap">
                {{ isNight ? 'Resting' : 'Available to Chat' }}
              </span>
            </div>
            
            <div class="hidden sm:block text-white/60 text-[10px] uppercase tracking-widest font-black mt-1">
              NY: {{ nyTimeDisplay }} <span class="opacity-30 mx-1">|</span> {{ formattedHoursDiff }}
            </div>
          </div>
        </div>

        <button class="md:hidden p-2 ml-1 hover:bg-white/10 rounded-lg transition-colors" @click="mobileOpen = !mobileOpen">
          <svg v-if="!mobileOpen" xmlns="http://www.w3.org/2000/svg" class="h-6 w-6 text-white" fill="none" viewBox="0 0 24 24" stroke="currentColor">
            <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M4 6h16M4 12h16M4 18h16" />
          </svg>
          <svg v-else xmlns="http://www.w3.org/2000/svg" class="h-6 w-6 text-white" fill="none" viewBox="0 0 24 24" stroke="currentColor">
            <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M6 18L18 6M6 6l12 12" />
          </svg>
        </button>
      </div>
    </div>

    <transition name="fade">
      <div v-show="mobileOpen" class="md:hidden absolute top-full left-0 w-full bg-[#5D6F82] border-b border-white/10 shadow-2xl">
        <div class="px-6 py-8 flex flex-col gap-6 text-center">
          <a href="#about" @click="mobileOpen = false" class="text-xl font-semibold text-white">About</a>
          <a href="#projects" @click="mobileOpen = false" class="text-xl font-semibold text-white">Projects</a>
          <a href="#contact" @click="mobileOpen = false" class="text-xl font-semibold text-white">Contact</a>
          <div class="h-[1px] bg-white/10 w-full my-2"></div>
          <div class="text-white/50 text-sm">Based in New York — {{ nyTimeDisplay }} ({{ formattedHoursDiff }})</div>
        </div>
      </div>
    </transition>
  </nav>
</template>

<script setup lang="ts">
import { ref, computed, onMounted, onUnmounted } from 'vue'
import TetrisLogo from './TetrisLogo.vue'

const ownerTimeZone = 'America/New_York'
const mobileOpen = ref(false)
const now = ref(new Date())

let timer: any
onMounted(() => {
  timer = setInterval(() => { now.value = new Date() }, 30000)
})
onUnmounted(() => clearInterval(timer))

const nyTimeDisplay = computed(() => {
  return new Intl.DateTimeFormat('en-US', {
    timeZone: ownerTimeZone,
    hour: 'numeric',
    minute: '2-digit',
    hour12: true
  }).format(now.value)
})

const isNight = computed(() => {
  const hour = parseInt(new Intl.DateTimeFormat('en-US', {
    timeZone: ownerTimeZone,
    hour: '2-digit',
    hour12: false
  }).format(now.value))
  return hour < 8 || hour >= 20
})

function getOffset(tz: string) {
  const date = new Date()
  const tzDate = new Date(date.toLocaleString('en-US', { timeZone: tz }))
  const utcDate = new Date(date.toLocaleString('en-US', { timeZone: 'UTC' }))
  return (tzDate.getTime() - utcDate.getTime()) / (1000 * 60 * 60)
}

const viewerTimeZone = Intl.DateTimeFormat().resolvedOptions().timeZone || 'UTC'
const hoursDiff = Math.round(getOffset(ownerTimeZone) - getOffset(viewerTimeZone))

const formattedHoursDiff = computed(() => {
  const absDiff = Math.abs(hoursDiff)
  if (hoursDiff === 0) return "Same Time"
  return hoursDiff > 0 ? `${absDiff}h ahead` : `${absDiff}h behind`
})
</script>

<style scoped>
.nav-link {
  color: rgba(255, 255, 255, 0.85);
  font-weight: 500;
  font-size: 1.1rem;
  transition: all 0.3s ease;
  position: relative;
}

.nav-link:hover { color: white; }
.nav-link::after {
  content: ''; position: absolute; width: 0%; height: 2px;
  bottom: -4px; left: 0; background-color: white; transition: width 0.3s ease;
}
.nav-link:hover::after { width: 100%; }

@keyframes sun-glow {
  0%, 100% { transform: scale(1); opacity: 1; }
  50% { transform: scale(1.1); filter: drop-shadow(0 0 5px rgba(255,255,255,0.4)); }
}
.animate-sun-glow { animation: sun-glow 4s ease-in-out infinite; }

.zzz {
  position: absolute; font-family: sans-serif; font-weight: 900;
  color: white; opacity: 0; animation: rise 4s ease-out infinite;
}
.z1 { top: -8px; right: -2px; font-size: 10px; animation-delay: 0s; }
.z2 { top: -14px; right: -8px; font-size: 7px; animation-delay: 2s; }

@keyframes rise {
  0% { transform: translateY(4px) translateX(0); opacity: 0; }
  20% { opacity: 0.8; }
  100% { transform: translateY(-12px) translateX(8px); opacity: 0; }
}

.fade-enter-active, .fade-leave-active { transition: opacity 0.3s, transform 0.3s; }
.fade-enter-from, .fade-leave-to { opacity: 0; transform: translateY(-10px); }
</style>