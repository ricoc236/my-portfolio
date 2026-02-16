<script setup lang="ts">
import Navbar from './components/Navbar.vue';
import Aboutme from './components/About/Aboutme.vue';
import project from './components/Projects/Project.vue';
import Contactme from './components/Contact/Contactme.vue';
</script>

<template>
  <header>
    <Navbar ownerTimeZone="America/New_York" />
    
  </header>

  <main>
    <div>
      <aboutme />
    </div>
    <div>
      <project />
    </div>
    <div>
    <Contactme />
    </div>
  
  </main>
</template>

