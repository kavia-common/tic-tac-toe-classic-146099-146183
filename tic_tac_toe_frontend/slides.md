---
# Global deck settings
theme: default
title: "Tic Tac Toe"
info: |
  Ocean Professional themed interactive Tic Tac Toe game
class: text-left
mdc: true
transition: slide-left
fonts:
  sans: Inter, ui-sans-serif, system-ui, -apple-system, Segoe UI, Roboto, Helvetica Neue, Arial
  mono: ui-monospace, SFMono-Regular, Menlo, Monaco, Consolas, "Liberation Mono", "Courier New", monospace
css: |
  @import "./style.css";
---

# Tic Tac Toe

<script setup lang="ts">
import { ref, computed } from 'vue'

type Player = 'X' | 'O' | null

// Game state
const board = ref<Player[]>(Array(9).fill(null))
const xIsNext = ref(true)
const winner = ref<Player>(null)
const isDraw = ref(false)

// Lines for 3x3
const winningLines: number[][] = [
  [0,1,2],[3,4,5],[6,7,8], // rows
  [0,3,6],[1,4,7],[2,5,8], // cols
  [0,4,8],[2,4,6]          // diagonals
]

// PUBLIC_INTERFACE
function handleCellClick(index: number) {
  /** Handle a user click on a cell: place mark if allowed, then evaluate winner/draw. */
  if (winner.value || board.value[index]) return
  board.value[index] = xIsNext.value ? 'X' : 'O'
  xIsNext.value = !xIsNext.value
  evaluateGame()
}

// PUBLIC_INTERFACE
function resetGame() {
  /** Reset all game state to initial values. */
  board.value = Array(9).fill(null)
  xIsNext.value = true
  winner.value = null
  isDraw.value = false
}

// Check for winner or draw
function evaluateGame() {
  for (const [a,b,c] of winningLines) {
    const va = board.value[a], vb = board.value[b], vc = board.value[c]
    if (va && va === vb && va === vc) {
      winner.value = va
      isDraw.value = false
      return
    }
  }
  if (board.value.every(c => c !== null)) {
    isDraw.value = true
    winner.value = null
  }
}

const currentPlayer = computed<Player>(() => (xIsNext.value ? 'X' : 'O'))

const statusText = computed(() => {
  if (winner.value) return `Winner: ${winner.value}`
  if (isDraw.value) return 'Draw game'
  return `Turn: ${currentPlayer.value}`
})
</script>

<div class="game-container">
  <div class="game-card">
    <div class="game-header">
      <div class="overline">Ocean Professional</div>
      <h2 class="text-hero">Tic Tac Toe</h2>
      <p class="muted">Two-player local game • 3x3 grid</p>
    </div>

    <div class="status-row">
      <div class="status-pill" :data-state="winner ? 'win' : (isDraw ? 'draw' : 'turn')">
        <!-- Replace status text to show icons while keeping computed logic intact -->
        <template v-if="winner">
          Winner:
          <span v-if="winner === 'X'" class="piece knight piece-inline">♞</span>
          <span v-else class="piece queen piece-inline">♛</span>
        </template>
        <template v-else-if="isDraw">
          Draw game
        </template>
        <template v-else>
          Turn:
          <span v-if="currentPlayer === 'X'" class="piece knight piece-inline">♞</span>
          <span v-else class="piece queen piece-inline">♛</span>
        </template>
      </div>
    </div>

    <div class="board">
      <button
        v-for="(cell, idx) in board"
        :key="idx"
        class="cell"
        :class="{'cell-x': cell==='X', 'cell-o': cell==='O'}"
        @click="handleCellClick(idx)"
        :aria-label="`Cell ${idx+1}`"
      >
        <span v-if="cell" class="mark" :class="{'is-piece': true}">
          <template v-if="cell === 'X'">
            <!-- Chess Knight for X -->
            <span class="piece knight" aria-hidden="true">♞</span>
            <span class="sr-only">Knight</span>
          </template>
          <template v-else>
            <!-- Chess Queen for O -->
            <span class="piece queen" aria-hidden="true">♛</span>
            <span class="sr-only">Queen</span>
          </template>
        </span>
      </button>
    </div>

    <div class="actions">
      <button class="btn-primary" @click="resetGame">New Game</button>
      <div class="legend">
        <span class="badge x"><span class="piece knight">♞</span></span> goes first •
        <span class="badge o"><span class="piece queen">♛</span></span> follows
      </div>
    </div>
  </div>
</div>

<style>
/* Ocean Professional adaptation: light modern aesthetic using provided palette */
:root {
  --op-primary: #2563EB; /* blue */
  --op-amber: #F59E0B;   /* amber */
  --op-error: #EF4444;
  --op-bg: #f9fafb;
  --op-surface: #ffffff;
  --op-text: #111827;

  --op-border: #E5E7EB;
  --op-shadow: 0 10px 24px rgba(0,0,0,0.06);
  --op-shadow-strong: 0 16px 40px rgba(37,99,235,0.12);
}

.slidev-page, .slidev-layout {
  background: var(--op-bg) !important;
  color: var(--op-text);
}

/* Game container centered layout */
.game-container {
  min-height: calc(100vh - 80px);
  display: grid;
  align-items: center;
  justify-items: center;
  padding: 24px;
  background-image: radial-gradient(800px 400px at 60% -10%, rgba(37,99,235,0.08), rgba(0,0,0,0));
}

/* Card surface */
.game-card {
  width: min(920px, 96vw);
  display: grid;
  gap: 18px;
  background: var(--op-surface);
  border: 1px solid var(--op-border);
  border-radius: 20px;
  padding: 20px;
  box-shadow: var(--op-shadow);
}

/* Header */
.game-header .text-hero {
  background: linear-gradient(135deg, var(--op-primary), #60A5FA);
  -webkit-background-clip: text;
  background-clip: text;
  color: transparent;
  margin: 4px 0 2px;
}

/* Status */
.status-row {
  display: grid;
  justify-items: center;
}
.status-pill {
  display: inline-flex;
  align-items: center;
  justify-content: center;
  padding: 10px 14px;
  border-radius: 999px;
  font-weight: 700;
  border: 1px solid var(--op-border);
  color: var(--op-text);
  background: #ffffff;
  box-shadow: var(--op-shadow);
}
.status-pill[data-state="win"] {
  background: linear-gradient(135deg, rgba(37,99,235,0.08), rgba(245,158,11,0.10));
  border-color: #DBEAFE;
}
.status-pill[data-state="draw"] {
  background: linear-gradient(135deg, rgba(17,24,39,0.04), rgba(17,24,39,0.06));
}

/* Board: centered square, responsive */
.board {
  margin-inline: auto;
  display: grid;
  grid-template-columns: repeat(3, minmax(0, 1fr));
  gap: 10px;
  width: min(560px, 90vw);
  aspect-ratio: 1 / 1;
  background: linear-gradient(180deg, rgba(37,99,235,0.04), rgba(17,24,39,0.02));
  padding: 10px;
  border-radius: 16px;
  border: 1px solid var(--op-border);
  box-shadow: var(--op-shadow-strong);
}

/* Cells */
.cell {
  background: #ffffff;
  border: 1px solid var(--op-border);
  border-radius: 14px;
  box-shadow: 0 6px 16px rgba(0,0,0,0.06);
  cursor: pointer;
  display: grid;
  place-items: center;
  transition: transform 160ms ease, box-shadow 160ms ease, border-color 160ms ease, background 160ms ease;
  user-select: none;
  -webkit-tap-highlight-color: transparent;
}
.cell:hover {
  transform: translateY(-2px);
  box-shadow: 0 10px 24px rgba(0,0,0,0.08);
  border-color: #DBEAFE;
  background: linear-gradient(180deg, #fff, #F8FAFF);
}
.cell:active {
  transform: translateY(0);
}

/* Marks */
.mark {
  display: inline-flex;
  align-items: center;
  justify-content: center;
  line-height: 1;
}

/* Accessible SR-only text for icon labels */
.sr-only {
  position: absolute;
  width: 1px;
  height: 1px;
  padding: 0;
  margin: -1px;
  overflow: hidden;
  clip: rect(0,0,0,0);
  white-space: nowrap; /* added to prevent the text from wrapping */
  border: 0;
}

/* Chess piece styling */
.piece {
  font-size: clamp(34px, 8.2vw, 84px);
  font-weight: 700;
  letter-spacing: -0.02em;
  transform: translateZ(0); /* enable GPU hint for smoother hover */
}
.piece-inline {
  font-size: clamp(18px, 2.8vw, 28px);
  vertical-align: middle;
}

/* Colors mapped to original player accent colors */
.cell-x .piece,
.piece.knight {
  color: var(--op-primary);
  text-shadow: 0 6px 22px rgba(37,99,235,0.25);
}
.cell-o .piece,
.piece.queen {
  color: var(--op-amber);
  text-shadow: 0 6px 22px rgba(245,158,11,0.25);
}

/* Actions */
.actions {
  display: grid;
  grid-template-columns: auto 1fr;
  gap: 12px;
  align-items: center;
}
.actions .legend {
  color: #6B7280;
}

/* Badges */
.badge {
  display: inline-flex;
  align-items: center;
  justify-content: center;
  width: 22px;
  height: 22px;
  border-radius: 999px;
  font-size: 12px;
  font-weight: 800;
  color: #ffffff;
  box-shadow: 0 6px 16px rgba(0,0,0,0.08);
}
.badge.x { background: var(--op-primary); }
.badge.o { background: var(--op-amber); }

/* Primary button refinement to match Ocean Professional */
.btn-primary {
  background: var(--op-primary);
  color: #fff;
  border: 0;
  border-radius: 12px;
  padding: 12px 18px;
  font-weight: 800;
  box-shadow: 0 8px 22px rgba(37,99,235,0.28);
}
.btn-primary:hover {
  filter: brightness(1.05);
  transform: translateY(-1px);
}
.btn-primary:active {
  transform: translateY(0);
}

@media (max-width: 640px) {
  .game-card { padding: 16px; border-radius: 16px; }
  .actions { grid-template-columns: 1fr; justify-items: center; }
}
</style>
