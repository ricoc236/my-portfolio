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
	{ title: 'TCP MultiUser ChatRoom', description: 'A multithreaded C++ server that manages concurrent client connections via TCP sockets, enabling real-time, terminal-based communication using raw byte streams.', tags: ['Socket API', 'C++', 'POSIX Threads', 'TCP protocol stack', 'Client Server Model'], url: '#' },
	{ title: 'PlayBoy', description: 'A community-driven platform where users can share Spotify tracks, join real-time music discussions, and build personalized profiles. Using RESTful PHP API and MySQL to manage user generated content, social interactions, and community feeds.', tags: ['React', 'PHP', 'tailwind', 'MySQL', 'Figma', 'scrum', 'agile'], url: '#' },
	{ title: '8San', description: 'Image sharing Platform that allows user to share and rate images. Developed a secure upload pipeline using FFmpeg to detect and verify file integrity at the binary level and Pillow for automated image manipulation, metadata extraction, and thumbnail generation.', tags: ['Flask', 'CSS', 'JavaScript', 'FFmpeg', 'MongoDB', 'Docker'], url: '#' },
	{ title: 'AR WhiteBoard', description: 'An augmented reality whiteboard that allows users to draw on a digital canvas with hand gestures.', tags: ['OpenCV', 'MediaPipe', 'NumPy', 'Python', 'Coordinate Mapping'], url: '#' },

]
</script>

<style scoped>

</style>

