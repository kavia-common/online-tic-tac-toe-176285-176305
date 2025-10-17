<script setup lang="ts">
import { computed, reactive } from 'vue'
import Cell from './Cell.vue'

type Player = 'X' | 'O'
type Mark = Player | null

// Board state: 9 cells
const state = reactive({
  board: Array<Mark>(9).fill(null),
  currentPlayer: 'X' as Player,
  winner: null as Player | null,
  isDraw: false,
  gameOver: false,
})

const WIN_LINES: number[][] = [
  [0, 1, 2],
  [3, 4, 5],
  [6, 7, 8],
  [0, 3, 6],
  [1, 4, 7],
  [2, 5, 8],
  [0, 4, 8],
  [2, 4, 6],
]

function checkWinner(board: Mark[]): Player | null {
  for (const [a, b, c] of WIN_LINES) {
    if (board[a] && board[a] === board[b] && board[a] === board[c]) {
      return board[a] as Player
    }
  }
  return null
}

function checkDraw(board: Mark[]): boolean {
  return board.every((c) => c !== null)
}

// PUBLIC_INTERFACE
function handleCellClick(index: number) {
  /** Handles a user move: sets mark, checks win/draw, toggles player. */
  if (state.gameOver || state.board[index] !== null) return
  state.board[index] = state.currentPlayer

  const maybeWinner = checkWinner(state.board)
  if (maybeWinner) {
    state.winner = maybeWinner
    state.gameOver = true
    return
  }

  if (checkDraw(state.board)) {
    state.isDraw = true
    state.gameOver = true
    return
  }

  state.currentPlayer = state.currentPlayer === 'X' ? 'O' : 'X'
}

// PUBLIC_INTERFACE
function restartGame() {
  /** Resets the game state to initial values. */
  state.board = Array<Mark>(9).fill(null)
  state.currentPlayer = 'X'
  state.winner = null
  state.isDraw = false
  state.gameOver = false
}

const statusText = computed(() => {
  // Keep narrative but avoid announcing single letters only
  if (state.winner) return `Winner: ${state.winner === 'X' ? 'Knight' : 'Queen'}`
  if (state.isDraw) return 'Draw!'
  return `Current Player: ${state.currentPlayer === 'X' ? 'Knight' : 'Queen'}`
})

const statusClass = computed(() => {
  if (state.winner) return 'status status--winner'
  if (state.isDraw) return 'status status--draw'
  return 'status'
})

// Small helpers for chip visuals and a11y labels
const currentPlayerGlyph = computed(() => (state.currentPlayer === 'X' ? '♞' : '♛'))
const currentPlayerLabel = computed(() => (state.currentPlayer === 'X' ? 'Knight' : 'Queen'))
</script>

<template>
  <section class="board-card" aria-label="Tic Tac Toe game board">
    <div class="status-bar">
      <div
        class="chip"
        :class="state.currentPlayer === 'X' ? 'chip--x' : 'chip--o'"
        role="img"
        :aria-label="currentPlayerLabel"
        :title="currentPlayerLabel"
      >
        {{ currentPlayerGlyph }}
      </div>
      <p :class="statusClass" role="status" aria-live="polite">{{ statusText }}</p>
    </div>

    <div
      class="grid"
      role="grid"
      aria-label="3 by 3 tic tac toe grid"
      :aria-disabled="state.gameOver ? 'true' : 'false'"
    >
      <Cell
        v-for="(mark, idx) in state.board"
        :key="idx"
        :index="idx"
        :mark="mark"
        :disabled="state.gameOver || mark !== null"
        :aria-label="`Cell ${idx + 1}, ${mark ? (mark === 'X' ? 'Knight' : 'Queen') : 'empty'}`"
        @select="handleCellClick"
      />
    </div>

    <div class="controls">
      <button
        class="btn"
        type="button"
        @click="restartGame"
        aria-label="Restart Game"
        title="Restart Game"
      >
        Restart Game
      </button>
    </div>
  </section>
</template>

<style scoped>
.board-card {
  width: 100%;
  max-width: 520px;
  background: var(--ocean-surface);
  border-radius: 16px;
  padding: 1.25rem;
  box-shadow: 0 10px 25px rgba(2, 6, 23, 0.08);
  border: 1px solid rgba(2, 6, 23, 0.06);
}

.status-bar {
  display: flex;
  align-items: center;
  gap: 0.75rem;
  margin-bottom: 1rem;
}

.chip {
  width: 36px;
  height: 36px;
  border-radius: 10px;
  display: grid;
  place-items: center;
  font-weight: 800;
  letter-spacing: 0;
  color: white;
  box-shadow: inset 0 1px 0 rgba(255,255,255,0.2);
  font-size: 1.1rem;
  line-height: 1;
}
.chip--x { background: var(--ocean-primary); }
.chip--o { background: var(--ocean-secondary); }

.status {
  font-weight: 600;
  color: var(--ocean-text);
}
.status--winner {
  color: var(--ocean-success);
}
.status--draw {
  color: #374151;
}

.grid {
  margin-top: 0.5rem;
  display: grid;
  grid-template-columns: repeat(3, 1fr);
  gap: 10px;
  background: linear-gradient(135deg, rgba(59,130,246,0.08), rgba(243,244,246,0.8));
  padding: 10px;
  border-radius: 14px;
  border: 1px solid rgba(2, 6, 23, 0.06);
}

.controls {
  display: flex;
  justify-content: center;
  margin-top: 1rem;
}

.btn {
  background: var(--ocean-primary);
  color: white;
  font-weight: 600;
  padding: 0.7rem 1.1rem;
  border-radius: 12px;
  border: none;
  box-shadow: 0 6px 16px rgba(37, 99, 235, 0.25);
  transition: transform 0.08s ease, box-shadow 0.2s ease, opacity 0.2s ease;
  cursor: pointer;
}
.btn:hover { box-shadow: 0 8px 22px rgba(37, 99, 235, 0.32); }
.btn:active { transform: translateY(1px) scale(0.99); }
.btn:focus-visible {
  outline: 3px solid rgba(37,99,235,0.4);
  outline-offset: 2px;
}

@media (max-width: 420px) {
  .board-card { padding: 1rem; }
  .grid { gap: 8px; padding: 8px; }
}
</style>
