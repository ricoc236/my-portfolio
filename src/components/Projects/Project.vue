<template>
	<section id="projects" class="py-12">
		<div class="max-w-6xl mx-auto px-6">
			<h2 class="text-2xl font-bold text-white mb-6">Projects</h2>

			<div class="grid grid-cols-1 md:grid-cols-2 gap-6">
				<ProjectCard v-for="p in projects" :key="p.title" v-bind="p" />
			</div>
		</div>
	</section>
</template>

<script setup lang="ts">
import ProjectCard from './ProjectCard.vue'

const projects = [
	{ title: 'TCP MultiUser ChatRoom', description: 'Chatroom using Sockets for real-time communication in the terminal.', tags: ['Socket API', 'C++'], url: '#' },

]
</script>

<style scoped>

</style>

