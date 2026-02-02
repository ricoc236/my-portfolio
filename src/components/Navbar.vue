

<template>
	<nav class="w-full backdrop-blur border-b" style="--brand: #82C8E5; background-color: var(--brand);">
		<div class="max-w-5xl mx-auto px-4 py-3 flex items-center justify-between">

			<div class="flex items-center gap-3">
				<div class="w-10 h-10 rounded-md logo flex items-center justify-center font-bold">LP</div>
				<span class="font-semibold text-white">My Portfolio</span>
			</div>

			<div class="flex-1 hidden md:flex justify-center">
				<ul class="flex items-center gap-6">
					<li>
						<a href="#about" class="nav-link">About</a>
					</li>
					<li>
						<a href="#projects" class="nav-link">Projects</a>
					</li>
					<li>
						<a href="#contact" class="nav-link">Contact</a>
					</li>
				</ul>
			</div>

			<div class="flex items-center gap-3">
				<div class="hidden md:block text-sm text-white text-right">
					<div v-if="hoursDiff === 0">Same timezone</div>
					<div v-else>{{ formattedHoursDiff }}</div>
					<div class="text-xs opacity-80">You ↔ {{ ownerTimeZone }}</div>
				</div>

				<button class="md:hidden p-2" @click="mobileOpen = !mobileOpen" aria-label="Toggle menu">
					<svg v-if="!mobileOpen" xmlns="http://www.w3.org/2000/svg" class="h-6 w-6 text-white" fill="none" viewBox="0 0 24 24" stroke="currentColor">
						<path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M4 6h16M4 12h16M4 18h16" />
					</svg>
					<svg v-else xmlns="http://www.w3.org/2000/svg" class="h-6 w-6 text-white" fill="none" viewBox="0 0 24 24" stroke="currentColor">
						<path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M6 18L18 6M6 6l12 12" />
					</svg>
				</button>
			</div>
		</div>


		<div v-show="mobileOpen" class="md:hidden bg-[color:var(--brand)]/95">
			<div class="px-4 pb-4">
				<a href="#about" class="block py-2 nav-link">About</a>
				<a href="#projects" class="block py-2 nav-link">Projects</a>
				<a href="#contact" class="block py-2 nav-link">Contact</a>


				<div class="mt-2 pt-2 border-t border-white/20">
					<div class="text-sm text-white font-semibold">Contact</div>
					<a href="ricosc04@gmail.com" class="block text-sm py-1 text-white/90">Email</a>
					<a href="https://www.linkedin.com/in/rico-chen-5996962ab" target="_blank" rel="noopener" class="block text-sm py-1 text-white/90">LinkedIn</a>
					<a href="https://github.com/ricoc236" target="_blank" rel="noopener" class="block text-sm py-1 text-white/90">GitHub</a>
				</div>
			</div>
		</div>
	</nav>
</template>

<script setup lang="ts">
import { ref, computed } from 'vue'

const props = defineProps<{ ownerTimeZone?: string }>()
const ownerTimeZone = props.ownerTimeZone ?? 'UTC'

const mobileOpen = ref(false)

function tzOffsetHours(timeZone: string): number {
  const now = new Date()
  const fmt = new Intl.DateTimeFormat('en-US', {
    timeZone,
    hour12: false,
    year: 'numeric', month: '2-digit', day: '2-digit',
    hour: '2-digit', minute: '2-digit', second: '2-digit'
  })
  const parts = fmt.formatToParts(now).reduce((acc: Record<string,string>, p) => {
    if (p.type !== 'literal') acc[p.type] = p.value
    return acc
  }, {})

  const tzString = `${parts.year}-${parts.month}-${parts.day}T${parts.hour}:${parts.minute}:${parts.second}Z`
  const tzDate = new Date(tzString)
  const offsetHours = - (tzDate.getTime() - now.getTime()) / (1000 * 60 * 60)
  return offsetHours
}

const viewerTimeZone = Intl.DateTimeFormat().resolvedOptions().timeZone || 'UTC'

const ownerOffset = tzOffsetHours(ownerTimeZone)
const viewerOffset = tzOffsetHours(viewerTimeZone)

const hoursDiff = Math.round((ownerOffset - viewerOffset) * 10) / 10

const formattedHoursDiff = computed(() => {
  const h = Math.abs(hoursDiff)
  const sign = hoursDiff > 0 ? `You are ${h}h behind` : `You are ${h}h ahead`
  return sign
})
</script>

<style scoped>

.logo{ background-color: var(--brand); }
.nav-link{ color: #334155; }
.nav-link:hover{ color: var(--brand); }

[v-cloak] { display: none; }
</style>
