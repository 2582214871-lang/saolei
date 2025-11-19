<template>
  <div class="game-board">
    <div class="game-container">
      <div class="game-header">
        <div class="game-info">
          <div class="info-item">
            <div class="info-icon">💣</div>
            <div class="info-content">
              <div class="info-label">地雷数</div>
              <div class="info-value">{{ mineCount }}</div>
            </div>
          </div>
          <div class="info-item">
            <div class="info-icon">🚩</div>
            <div class="info-content">
              <div class="info-label">剩余标记</div>
              <div class="info-value">{{ remainingFlags }}</div>
            </div>
          </div>
        </div>
        
        <div class="game-status">
          <div class="status-icon" :class="{ 'game-over': gameOver, 'game-won': gameWon }">
            {{ gameStatusIcon }}
          </div>
          <div class="status-text">{{ gameStatusText }}</div>
        </div>
        
        <div class="game-timer">
          <div class="timer-icon">⏱️</div>
          <div class="timer-display">{{ formattedTime }}</div>
        </div>
      </div>
      
      <div class="game-controls">
        <div class="difficulty-info">
          <span class="difficulty-label">难度：</span>
          <span class="difficulty-value">{{ difficultyText }}</span>
        </div>
        <button class="restart-button" @click="restartGame">
          <span class="button-icon">🔄</span>
          重新开始
        </button>
        <button class="home-button" @click="goHome">
          <span class="button-icon">🏠</span>
          返回首页
        </button>
      </div>
      
      <div class="board-container">
        <div class="game-board-grid" :style="gridStyle">
          <Cell
            v-for="(cell, index) in board"
            :key="index"
            :cell="cell"
            :index="index"
            @click="handleCellClick"
            @contextmenu="handleRightClick"
          />
        </div>
      </div>
      
      <div class="game-instructions">
        <div class="instruction-item">
          <span class="instruction-icon">👆</span>
          <span class="instruction-text">左键点击揭开格子</span>
        </div>
        <div class="instruction-item">
          <span class="instruction-icon">🚩</span>
          <span class="instruction-text">右键标记地雷位置</span>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import Cell from './Cell.vue';

export default {
  name: 'GameBoard',
  components: {
    Cell
  },
  props: {
    board: {
      type: Array,
      required: true
    },
    mineCount: {
      type: Number,
      required: true
    },
    remainingFlags: {
      type: Number,
      required: true
    },
    time: {
      type: Number,
      default: 0
    },
    gameOver: {
      type: Boolean,
      default: false
    },
    gameWon: {
      type: Boolean,
      default: false
    },
    difficulty: {
      type: String,
      default: 'easy'
    }
  },
  computed: {
    gridStyle() {
      const rows = this.board.length > 0 ? Math.sqrt(this.board.length) : 9;
      const cols = rows;
      
      return {
        gridTemplateColumns: `repeat(${cols}, minmax(0, 1fr))`,
        gridTemplateRows: `repeat(${rows}, minmax(0, 1fr))`
      };
    },
    formattedTime() {
      const minutes = Math.floor(this.time / 60);
      const seconds = this.time % 60;
      return `${minutes.toString().padStart(2, '0')}:${seconds.toString().padStart(2, '0')}`;
    },
    gameStatusIcon() {
      if (this.gameWon) return '😎';
      if (this.gameOver) return '😵';
      return '🙂';
    },
    gameStatusText() {
      if (this.gameWon) return '游戏胜利！';
      if (this.gameOver) return '游戏失败！';
      return '游戏进行中';
    },
    difficultyText() {
      switch (this.difficulty) {
        case 'easy':
          return '初级 (9×9, 10雷)';
        case 'medium':
          return '中级 (16×16, 40雷)';
        case 'hard':
          return '高级 (16×30, 99雷)';
        default:
          return '初级 (9×9, 10雷)';
      }
    }
  },
  methods: {
    handleCellClick(index) {
      this.$emit('cell-click', index);
    },
    handleRightClick(index) {
      this.$emit('right-click', index);
    },
    restartGame() {
      this.$emit('restart');
    },
    goHome() {
      this.$emit('go-home');
    }
  }
}
</script>

<style scoped>
.game-board {
  display: flex;
  justify-content: center;
  align-items: center;
  min-height: 100vh;
  background: linear-gradient(135deg, var(--gray-50) 0%, var(--primary-light) 100%);
  padding: var(--spacing-4);
}

.game-container {
  width: 100%;
  max-width: 800px;
  background-color: var(--white);
  border-radius: var(--radius-2xl);
  box-shadow: var(--shadow-xl);
  padding: var(--spacing-6);
  animation: fadeIn var(--transition-normal) ease-out;
}

.game-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-bottom: var(--spacing-6);
  padding: var(--spacing-4);
  background-color: var(--gray-50);
  border-radius: var(--radius-xl);
  box-shadow: var(--shadow-sm);
}

.game-info {
  display: flex;
  gap: var(--spacing-6);
}

.info-item {
  display: flex;
  align-items: center;
  gap: var(--spacing-2);
}

.info-icon {
  font-size: var(--text-2xl);
}

.info-content {
  display: flex;
  flex-direction: column;
  align-items: flex-start;
}

.info-label {
  font-size: var(--text-xs);
  color: var(--gray-600);
  text-transform: uppercase;
  letter-spacing: 0.05em;
}

.info-value {
  font-size: var(--text-xl);
  font-weight: 700;
  color: var(--gray-800);
}

.game-status {
  display: flex;
  flex-direction: column;
  align-items: center;
  gap: var(--spacing-1);
}

.status-icon {
  font-size: var(--text-4xl);
  transition: transform var(--transition-fast);
}

.status-icon.game-over {
  animation: shake 0.5s;
}

.status-icon.game-won {
  animation: bounce 1s;
}

.status-text {
  font-size: var(--text-sm);
  font-weight: 600;
  color: var(--gray-700);
}

.game-timer {
  display: flex;
  align-items: center;
  gap: var(--spacing-2);
}

.timer-icon {
  font-size: var(--text-2xl);
}

.timer-display {
  font-size: var(--text-xl);
  font-weight: 700;
  color: var(--gray-800);
  font-family: var(--font-family-mono);
  min-width: 60px;
  text-align: center;
}

.game-controls {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-bottom: var(--spacing-6);
  padding: var(--spacing-4);
  background-color: var(--gray-50);
  border-radius: var(--radius-xl);
  box-shadow: var(--shadow-sm);
}

.difficulty-info {
  display: flex;
  align-items: center;
  gap: var(--spacing-2);
}

.difficulty-label {
  font-size: var(--text-base);
  color: var(--gray-600);
}

.difficulty-value {
  font-size: var(--text-base);
  font-weight: 600;
  color: var(--primary-color);
}

.restart-button, .home-button {
  display: flex;
  align-items: center;
  gap: var(--spacing-2);
  padding: var(--spacing-2) var(--spacing-4);
  font-size: var(--text-base);
  font-weight: 600;
  border-radius: var(--radius-lg);
  transition: all var(--transition-fast);
  cursor: pointer;
}

.restart-button {
  background-color: var(--warning-color);
  color: var(--white);
}

.restart-button:hover {
  background-color: var(--warning-hover);
  transform: translateY(-2px);
  box-shadow: var(--shadow-md);
}

.home-button {
  background-color: var(--secondary-color);
  color: var(--white);
}

.home-button:hover {
  background-color: var(--secondary-hover);
  transform: translateY(-2px);
  box-shadow: var(--shadow-md);
}

.button-icon {
  font-size: var(--text-lg);
}

.board-container {
  display: flex;
  justify-content: center;
  margin-bottom: var(--spacing-6);
  padding: var(--spacing-4);
  background-color: var(--gray-100);
  border-radius: var(--radius-xl);
  box-shadow: inset var(--shadow-sm);
}

.game-board-grid {
  display: grid;
  gap: 2px;
  padding: var(--spacing-2);
  background-color: var(--gray-300);
  border-radius: var(--radius-lg);
  width: fit-content;
}

.game-instructions {
  display: flex;
  justify-content: center;
  gap: var(--spacing-8);
  padding: var(--spacing-4);
  background-color: var(--gray-50);
  border-radius: var(--radius-xl);
  box-shadow: var(--shadow-sm);
}

.instruction-item {
  display: flex;
  align-items: center;
  gap: var(--spacing-2);
}

.instruction-icon {
  font-size: var(--text-xl);
}

.instruction-text {
  font-size: var(--text-sm);
  color: var(--gray-600);
}

/* 动画 */
@keyframes shake {
  0%, 100% { transform: translateX(0); }
  10%, 30%, 50%, 70%, 90% { transform: translateX(-5px); }
  20%, 40%, 60%, 80% { transform: translateX(5px); }
}

@keyframes bounce {
  0%, 100% { transform: translateY(0); }
  50% { transform: translateY(-10px); }
}

/* 响应式设计 */
@media (max-width: 768px) {
  .game-container {
    padding: var(--spacing-4);
  }
  
  .game-header {
    flex-direction: column;
    gap: var(--spacing-4);
  }
  
  .game-info {
    width: 100%;
    justify-content: space-around;
  }
  
  .game-controls {
    flex-direction: column;
    gap: var(--spacing-4);
  }
  
  .game-instructions {
    flex-direction: column;
    gap: var(--spacing-2);
  }
  
  .board-container {
    padding: var(--spacing-2);
  }
}

@media (max-width: 640px) {
  .game-board {
    padding: var(--spacing-2);
  }
  
  .game-container {
    padding: var(--spacing-3);
  }
  
  .game-header, .game-controls, .game-instructions {
    padding: var(--spacing-3);
  }
  
  .info-value, .timer-display {
    font-size: var(--text-lg);
  }
  
  .status-icon {
    font-size: var(--text-3xl);
  }
  
  .game-board-grid {
    gap: 1px;
  }
}
</style>