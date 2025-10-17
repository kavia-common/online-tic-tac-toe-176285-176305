<script setup lang="ts">
import GameBoard from '@/components/GameBoard.vue'
</script>

<template>
  <main class="home-container">
    <GameBoard />
  </main>
</template>

<style scoped>
.home-container {
  min-height: calc(100vh - 160px);
  display: grid;
  place-items: center;
  padding: 1rem;
}
</style>
