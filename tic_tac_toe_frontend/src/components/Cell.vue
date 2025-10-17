<script setup lang="ts">
import { computed } from 'vue'

// Ensure multi-word component name to satisfy lint rule
defineOptions({
  name: 'TicCell',
})

const props = defineProps<{
  index: number
  mark: 'X' | 'O' | null
  disabled?: boolean
}>()

const emit = defineEmits<{
  (e: 'select', index: number): void
}>()

// Compute piece character and aria-label for accessibility
const pieceChar = computed(() => {
  if (props.mark === 'X') return '♞' // Knight
  if (props.mark === 'O') return '♛' // Queen
  return ''
})

const pieceLabel = computed(() => {
  if (props.mark === 'X') return 'Knight'
  if (props.mark === 'O') return 'Queen'
  return ''
})

const classes = computed(() => {
  return [
    'cell',
    props.mark === 'X' ? 'cell--x' : '',
    props.mark === 'O' ? 'cell--o' : '',
    props.disabled ? 'cell--disabled' : '',
  ].join(' ')
})

function onClick() {
  if (props.disabled) return
  emit('select', props.index)
}
</script>

<template>
  <button
    class="cell-wrapper"
    role="gridcell"
    type="button"
    :aria-disabled="disabled ? 'true' : 'false'"
    :disabled="disabled"
    @click="onClick"
  >
    <div :class="classes">
      <!-- Use emoji pieces for lightweight rendering; expose accessible label -->
      <span
        v-if="mark"
        class="symbol"
        role="img"
        :aria-label="pieceLabel"
      >
        {{ pieceChar }}
      </span>
    </div>
  </button>
</template>

<style scoped>
:root {
  /* Provide overridable sizing var for pieces */
  --piece-size: 2.5rem;
}

.cell-wrapper {
  appearance: none;
  background: transparent;
  border: none;
  padding: 0;
  cursor: pointer;
  outline: none;
  border-radius: 12px;
}

.cell-wrapper:focus-visible .cell {
  box-shadow: 0 0 0 3px rgba(37,99,235,0.35);
}

.cell-wrapper:disabled {
  cursor: default;
}

.cell {
  height: 110px;
  width: 110px;
  background: var(--ocean-surface);
  border-radius: 12px;
  border: 1px solid rgba(2, 6, 23, 0.06);
  display: grid;
  place-items: center;
  color: var(--ocean-text);
  transition: transform 0.08s ease, background-color 0.2s ease, box-shadow 0.2s ease, border-color 0.2s ease;
  box-shadow: 0 6px 16px rgba(2, 6, 23, 0.06);
}

.cell:hover {
  transform: translateY(-1px);
  border-color: rgba(37, 99, 235, 0.25);
}

.cell--disabled {
  opacity: 0.8;
}

.symbol {
  font-size: var(--piece-size);
  line-height: 1;
  font-weight: 800;
  letter-spacing: 0; /* chess glyphs don't need extra spacing */
  display: inline-grid;
  place-items: center;
  transform: translateZ(0);
  transition: color 0.2s ease, text-shadow 0.2s ease;
}

/* Theme colors for X (Knight) and O (Queen) */
.cell--x .symbol {
  color: var(--ocean-primary);
  text-shadow: 0 2px 10px rgba(37,99,235,0.25);
}

.cell--o .symbol {
  color: var(--ocean-secondary);
  text-shadow: 0 2px 10px rgba(245,158,11,0.25);
}

@media (max-width: 420px) {
  .cell { height: 88px; width: 88px; }
  .symbol { font-size: calc(var(--piece-size) * 0.88); }
}
</style>
