<template>
  <div 
    class="cell"
    :class="cellClasses"
    @click="handleClick"
    @contextmenu.prevent="handleRightClick"
  >
    <div class="cell-content">
      <span v-if="cell.isFlagged" class="cell-icon flag">🚩</span>
      <span v-else-if="cell.isRevealed && cell.isMine" class="cell-icon mine">💣</span>
      <span v-else-if="cell.isRevealed && cell.adjacentMines > 0" class="cell-number" :class="numberClass">{{ cell.adjacentMines }}</span>
    </div>
    <div v-if="!cell.isRevealed && !cell.isFlagged" class="cell-cover"></div>
  </div>
</template>

<script>
export default {
  name: 'Cell',
  props: {
    cell: {
      type: Object,
      required: true
    },
    index: {
      type: Number,
      required: true
    }
  },
  computed: {
    cellClasses() {
      return {
        'revealed': this.cell.isRevealed,
        'mine': this.cell.isMine,
        'flagged': this.cell.isFlagged,
        'exploded': this.cell.isMine && this.cell.isRevealed,
        'safe': this.cell.isRevealed && !this.cell.isMine
      };
    },
    numberClass() {
      return `number-${this.cell.adjacentMines}`;
    }
  },
  methods: {
    handleClick() {
      this.$emit('click', this.index);
    },
    handleRightClick() {
      this.$emit('contextmenu', this.index);
    }
  }
}
</script>

<style scoped>
.cell {
  position: relative;
  width: 30px;
  height: 30px;
  background-color: var(--gray-400);
  border: 1px solid var(--gray-500);
  border-radius: var(--radius-sm);
  cursor: pointer;
  overflow: hidden;
  transition: all var(--transition-fast);
  user-select: none;
}

.cell:hover:not(.revealed):not(.flagged) {
  background-color: var(--gray-300);
  transform: translateY(-1px);
  box-shadow: var(--shadow-sm);
}

.cell.revealed {
  background-color: var(--gray-200);
  border-color: var(--gray-300);
  cursor: default;
}

.cell.mine.exploded {
  background-color: var(--danger-light);
  animation: explode 0.5s;
}

.cell.flagged {
  background-color: var(--warning-light);
  animation: flagPlant 0.3s;
}

.cell-content {
  display: flex;
  justify-content: center;
  align-items: center;
  width: 100%;
  height: 100%;
  position: relative;
  z-index: 2;
}

.cell-icon {
  font-size: var(--text-lg);
  animation: fadeIn 0.3s;
}

.cell-number {
  font-weight: 700;
  font-size: var(--text-base);
  animation: fadeIn 0.3s;
}

/* 不同数字的颜色 */
.number-1 { color: var(--primary-color); }
.number-2 { color: var(--success-color); }
.number-3 { color: var(--danger-color); }
.number-4 { color: var(--secondary-color); }
.number-5 { color: var(--warning-color); }
.number-6 { color: var(--info-color); }
.number-7 { color: var(--gray-800); }
.number-8 { color: var(--gray-900); }

.cell-cover {
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background: linear-gradient(135deg, var(--gray-400) 0%, var(--gray-500) 100%);
  border-radius: var(--radius-sm);
  transition: all var(--transition-fast);
  z-index: 1;
}

.cell:hover:not(.revealed):not(.flagged) .cell-cover {
  background: linear-gradient(135deg, var(--gray-300) 0%, var(--gray-400) 100%);
}

.cell.revealed .cell-cover {
  opacity: 0;
  transform: scale(0.8);
}

.cell.flagged .cell-cover {
  background: linear-gradient(135deg, var(--warning-light) 0%, var(--warning-color) 100%);
  opacity: 0.7;
}

/* 动画 */
@keyframes fadeIn {
  from { opacity: 0; transform: scale(0.8); }
  to { opacity: 1; transform: scale(1); }
}

@keyframes explode {
  0% { transform: scale(1); }
  50% { transform: scale(1.2); }
  100% { transform: scale(1); }
}

@keyframes flagPlant {
  0% { transform: scale(1) rotate(0deg); }
  50% { transform: scale(1.1) rotate(5deg); }
  100% { transform: scale(1) rotate(0deg); }
}

/* 响应式设计 */
@media (max-width: 768px) {
  .cell {
    width: 25px;
    height: 25px;
  }
  
  .cell-icon {
    font-size: var(--text-base);
  }
  
  .cell-number {
    font-size: var(--text-sm);
  }
}

@media (max-width: 640px) {
  .cell {
    width: 22px;
    height: 22px;
  }
  
  .cell-icon {
    font-size: var(--text-sm);
  }
  
  .cell-number {
    font-size: var(--text-xs);
  }
}
</style>