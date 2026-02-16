<template>
	<article class="bg-white/5 border border-white/10 rounded-lg p-6 hover:shadow-lg transition-all">
		<div v-if="image" class="mb-4 overflow-hidden rounded-md">
			<img :src="image" alt="" class="w-full h-56 object-cover" />
		</div>

		<h3 class="text-white font-semibold text-xl">{{ title }}</h3>
		<p v-if="description" class="text-white/80 text-sm mt-3">{{ description }}</p>

		<div v-if="tags && tags.length" class="mt-4 flex flex-wrap gap-2">
			<span v-for="tag in tags" :key="tag" class="text-xs px-2 py-1 bg-white/10 rounded">{{ tag }}</span>
		</div>

		<a v-if="url" :href="url" target="_blank" rel="noopener" class="inline-block mt-4 text-sm text-white underline">View</a>
	</article>
</template>

<script setup lang="ts">
const props = defineProps<{
	title: string
	description?: string
	image?: string
	tags?: string[]
	url?: string
}>()
</script>

<style scoped>

</style>

