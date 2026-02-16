<template>
  <section class="py-16">
    <div class="max-w-6xl mx-auto px-6 flex flex-col md:flex-row items-center gap-12">
      
      <div class="md:w-1/2 w-full text-center md:text-left">
        <h2 class="text-4xl md:text-6xl font-bold mb-6 text-white">
          <span ref="typedTarget"></span>
        </h2>
        <p class="text-white/95 text-lg md:text-xl">
          I'm a passionate Software Engineer and forever learner. I love working on projects and problems that are challenging, meaningful and engaging; the types of problems that'll keep me up at night.
        </p>
        
        <div class="mt-6 flex flex-wrap items-center justify-center md:justify-start gap-4">
          <a href="https://github.com/ricoc236" target="_blank" class="social-link" aria-label="GitHub">
            <svg xmlns="http://www.w3.org/2000/svg" width="28" height="28" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"><path d="M15 22v-4a4.8 4.8 0 0 0-1-3.5c3 0 6-2 6-5.5.08-1.25-.27-2.48-1-3.5.28-1.15.28-2.35 0-3.5 0 0-1 0-3 1.5-2.64-.5-5.36-.5-8 0C6 2 5 2 5 2c-.3 1.15-.3 2.35 0 3.5A5.403 5.403 0 0 0 4 9c0 3.5 3 5.5 6 5.5-.39.49-.68 1.05-.85 1.65-.17.6-.22 1.23-.15 1.85v4"/><path d="M9 18c-4.51 2-5-2-7-2"/></svg>
          </a>

          <a href="https://www.linkedin.com/in/rico-chen-5996962ab" target="_blank" class="social-link" aria-label="LinkedIn">
            <svg xmlns="http://www.w3.org/2000/svg" width="28" height="28" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"><path d="M16 8a6 6 0 0 1 6 6v7h-4v-7a2 2 0 0 0-2-2 2 2 0 0 0-2 2v7h-4v-7a6 6 0 0 1 6-6z"/><rect width="4" height="12" x="2" y="9"/><circle cx="4" cy="4" r="2"/></svg>
          </a>

          <a href="/Rico-Chen-Resume.pdf" download="Rico-Chen-Resume.pdf" class="inline-flex items-center gap-2 text-white px-4 py-2 rounded hover:opacity-90 transition-opacity" style="background-color: #0f172a;">
            <svg xmlns="http://www.w3.org/2000/svg" class="h-5 w-5" viewBox="0 0 20 20" fill="currentColor" aria-hidden="true">
              <path fill-rule="evenodd" d="M3 3.5A1.5 1.5 0 014.5 2h11A1.5 1.5 0 0117 3.5V9a.5.5 0 01-1 0V3.5a.5.5 0 00-.5-.5h-11a.5.5 0 00-.5.5v13a.5.5 0 00.5.5H9a.5.5 0 010 1H4.5A1.5 1.5 0 013 16.5v-13z" clip-rule="evenodd" />
              <path d="M9 7a1 1 0 112 0v5.586l1.293-1.293a1 1 0 111.414 1.414l-3 3a1 1 0 01-1.414 0l-3-3a1 1 0 011.414-1.414L9 12.586V7z" />
            </svg>
            Resume
          </a>
        </div>
      </div>

<div class="md:w-1/2 w-full flex justify-center md:justify-end">
  <div class="relative group">
    
    <div class="absolute inset-0 rounded-full bg-white opacity-0 group-hover:opacity-20 blur-xl transition-opacity duration-500"></div>

    <div class="relative w-72 h-72 md:w-96 md:h-96 rounded-full overflow-hidden shadow-xl border-white transition-all duration-500 group-hover:shadow-[0_0_30px_rgba(255,255,255,0.3)]">
      
      <div class="absolute inset-0 rounded-full border-2 border-white z-10 pointer-events-none"></div>
      
      <img
        :src="imageSrc"
        :alt="alt"
        class="w-full h-full object-cover scale-125 translate-y-0 transition-transform duration-500 group-hover:scale-[1.35]"
      />
    </div>
  </div>
</div>

    </div>
  </section>
</template>

<script setup lang="ts">
import defaultImage from '../../assets/min_portfolio_image.png'
const props = withDefaults(defineProps<{ imageSrc?: string; alt?: string }>(), { imageSrc: defaultImage, alt: "Rico" })
const { imageSrc, alt } = props
import { ref, onMounted, onBeforeUnmount } from 'vue'
import Typed from 'typed.js'

const typedTarget = ref<HTMLElement | null>(null)
let typedInstance: Typed | null = null

onMounted(() => {
  if (typedTarget.value) {
    typedInstance = new Typed(typedTarget.value, {
      strings: ["Hi, I'm Rico"],
      typeSpeed: 80,
      showCursor: true,
      cursorChar: '|',
      loop: false,
      onComplete: (self) => {
        if (self.cursor) {
          self.cursor.style.display = 'none'
        }
      }
    })
  }
})

onBeforeUnmount(() => {
  if (typedInstance) {
    typedInstance.destroy()
  }
})
</script>

<style scoped>

h2 { color: #0f172a; }
p { max-width: 48rem; }

.social-link {
  color: #0f172a;
  transition: opacity 0.2s ease, transform 0.2s ease;
}

.social-link:hover {
  opacity: 0.8;
  transform: translateY(-2px);
}
</style>