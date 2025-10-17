<script setup lang="ts">
import GameBoard from '@/components/GameBoard.vue'
</script>

<template>
  <div class="app-shell">
    <header class="app-header">
      <h1 class="title">Tic Tac Toe</h1>
      <p class="subtitle">Two-player • Local</p>
    </header>

    <main class="app-main">
      <GameBoard />
    </main>

    <footer class="app-footer">
      <small>Built with Vue 3 • Ocean Professional theme</small>
    </footer>
  </div>
</template>

<style scoped>
.app-shell {
  min-height: 100vh;
  display: grid;
  grid-template-rows: auto 1fr auto;
  background: var(--ocean-background);
  color: var(--ocean-text);
}

.app-header {
  text-align: center;
  padding: 2rem 1rem 0.5rem;
}

.title {
  font-size: 1.875rem;
  font-weight: 700;
  color: var(--ocean-primary);
  letter-spacing: 0.2px;
}

.subtitle {
  color: #6b7280;
}

.app-main {
  display: grid;
  place-items: center;
  padding: 1rem;
}

.app-footer {
  text-align: center;
  padding: 1rem 0 2rem;
  color: #6b7280;
  font-size: 0.85rem;
}
</style>
