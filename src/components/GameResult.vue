<template>
  <div class="game-result">
    <div class="result-container">
      <div class="result-header">
        <div class="result-icon" :class="resultClass">
          {{ resultIcon }}
        </div>
        <h1 class="result-title">{{ resultTitle }}</h1>
        <p class="result-message">{{ resultMessage }}</p>
      </div>
      
      <div class="result-stats">
        <div class="stat-item">
          <div class="stat-icon">🎯</div>
          <div class="stat-content">
            <div class="stat-label">难度</div>
            <div class="stat-value">{{ difficultyText }}</div>
          </div>
        </div>
        
        <div class="stat-item">
          <div class="stat-icon">⏱️</div>
          <div class="stat-content">
            <div class="stat-label">用时</div>
            <div class="stat-value">{{ formattedTime }}</div>
          </div>
        </div>
        
        <div class="stat-item">
          <div class="stat-icon">💣</div>
          <div class="stat-content">
            <div class="stat-label">地雷数</div>
            <div class="stat-value">{{ mineCount }}</div>
          </div>
        </div>
        
        <div class="stat-item">
          <div class="stat-icon">🚩</div>
          <div class="stat-content">
            <div class="stat-label">标记数</div>
            <div class="stat-value">{{ flaggedCount }}</div>
          </div>
        </div>
      </div>
      
      <div class="result-actions">
        <button class="action-button primary" @click="playAgain">
          <span class="button-icon">🔄</span>
          再来一局
        </button>
        <button class="action-button secondary" @click="goHome">
          <span class="button-icon">🏠</span>
          返回首页
        </button>
      </div>
      
      <div v-if="isWin" class="achievement">
        <div class="achievement-icon">🏆</div>
        <div class="achievement-text">恭喜你成功扫除所有地雷！</div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: 'GameResult',
  props: {
    isWin: {
      type: Boolean,
      required: true
    },
    difficulty: {
      type: String,
      default: 'easy'
    },
    time: {
      type: Number,
      default: 0
    },
    mineCount: {
      type: Number,
      default: 0
    },
    flaggedCount: {
      type: Number,
      default: 0
    }
  },
  computed: {
    resultClass() {
      return this.isWin ? 'win' : 'lose';
    },
    resultIcon() {
      return this.isWin ? '🎉' : '💥';
    },
    resultTitle() {
      return this.isWin ? '游戏胜利！' : '游戏结束！';
    },
    resultMessage() {
      return this.isWin 
        ? '恭喜你成功扫除所有地雷！' 
        : '很遗憾，你踩到地雷了！';
    },
    formattedTime() {
      const minutes = Math.floor(this.time / 60);
      const seconds = this.time % 60;
      return `${minutes.toString().padStart(2, '0')}:${seconds.toString().padStart(2, '0')}`;
    },
    difficultyText() {
      switch (this.difficulty) {
        case 'easy':
          return '初级';
        case 'medium':
          return '中级';
        case 'hard':
          return '高级';
        default:
          return '初级';
      }
    }
  },
  methods: {
    playAgain() {
      this.$emit('play-again');
    },
    goHome() {
      this.$emit('go-home');
    }
  }
}
</script>

<style scoped>
.game-result {
  display: flex;
  justify-content: center;
  align-items: center;
  min-height: 100vh;
  background: linear-gradient(135deg, var(--gray-50) 0%, var(--primary-light) 100%);
  padding: var(--spacing-4);
}

.result-container {
  width: 100%;
  max-width: 500px;
  background-color: var(--white);
  border-radius: var(--radius-2xl);
  box-shadow: var(--shadow-xl);
  padding: var(--spacing-8);
  text-align: center;
  animation: slideUp 0.5s ease-out;
}

.result-header {
  margin-bottom: var(--spacing-8);
}

.result-icon {
  font-size: 5rem;
  margin-bottom: var(--spacing-4);
  animation: bounceIn 0.8s ease-out;
}

.result-icon.win {
  animation: bounceIn 0.8s ease-out, pulse 2s infinite;
}

.result-icon.lose {
  animation: shake 0.5s, bounceIn 0.8s ease-out;
}

.result-title {
  font-size: var(--text-3xl);
  font-weight: 700;
  color: var(--gray-800);
  margin-bottom: var(--spacing-2);
}

.result-message {
  font-size: var(--text-lg);
  color: var(--gray-600);
  margin-bottom: 0;
}

.result-stats {
  display: grid;
  grid-template-columns: repeat(2, 1fr);
  gap: var(--spacing-4);
  margin-bottom: var(--spacing-8);
}

.stat-item {
  display: flex;
  align-items: center;
  gap: var(--spacing-3);
  padding: var(--spacing-3);
  background-color: var(--gray-50);
  border-radius: var(--radius-lg);
  transition: transform var(--transition-fast);
}

.stat-item:hover {
  transform: translateY(-2px);
}

.stat-icon {
  font-size: var(--text-2xl);
}

.stat-content {
  display: flex;
  flex-direction: column;
  align-items: flex-start;
}

.stat-label {
  font-size: var(--text-sm);
  color: var(--gray-600);
  text-transform: uppercase;
  letter-spacing: 0.05em;
}

.stat-value {
  font-size: var(--text-lg);
  font-weight: 700;
  color: var(--gray-800);
}

.result-actions {
  display: flex;
  justify-content: center;
  gap: var(--spacing-4);
  margin-bottom: var(--spacing-6);
}

.action-button {
  display: flex;
  align-items: center;
  gap: var(--spacing-2);
  padding: var(--spacing-3) var(--spacing-6);
  font-size: var(--text-base);
  font-weight: 600;
  border-radius: var(--radius-lg);
  transition: all var(--transition-fast);
  cursor: pointer;
  border: none;
}

.action-button.primary {
  background-color: var(--primary-color);
  color: var(--white);
}

.action-button.primary:hover {
  background-color: var(--primary-hover);
  transform: translateY(-2px);
  box-shadow: var(--shadow-md);
}

.action-button.secondary {
  background-color: var(--secondary-color);
  color: var(--white);
}

.action-button.secondary:hover {
  background-color: var(--secondary-hover);
  transform: translateY(-2px);
  box-shadow: var(--shadow-md);
}

.button-icon {
  font-size: var(--text-lg);
}

.achievement {
  display: flex;
  align-items: center;
  justify-content: center;
  gap: var(--spacing-2);
  padding: var(--spacing-3);
  background: linear-gradient(135deg, var(--warning-light) 0%, var(--warning-color) 100%);
  border-radius: var(--radius-lg);
  color: var(--white);
  animation: fadeIn 1s ease-out 0.5s both;
}

.achievement-icon {
  font-size: var(--text-2xl);
  animation: spin 2s linear infinite;
}

.achievement-text {
  font-size: var(--text-base);
  font-weight: 600;
}

/* 动画 */
@keyframes slideUp {
  from { 
    opacity: 0; 
    transform: translateY(30px); 
  }
  to { 
    opacity: 1; 
    transform: translateY(0); 
  }
}

@keyframes bounceIn {
  0% { 
    opacity: 0; 
    transform: scale(0.3); 
  }
  50% { 
    opacity: 1; 
    transform: scale(1.05); 
  }
  70% { 
    transform: scale(0.9); 
  }
  100% { 
    transform: scale(1); 
  }
}

@keyframes shake {
  0%, 100% { transform: translateX(0); }
  10%, 30%, 50%, 70%, 90% { transform: translateX(-5px); }
  20%, 40%, 60%, 80% { transform: translateX(5px); }
}

@keyframes pulse {
  0% { transform: scale(1); }
  50% { transform: scale(1.05); }
  100% { transform: scale(1); }
}

@keyframes spin {
  from { transform: rotate(0deg); }
  to { transform: rotate(360deg); }
}

@keyframes fadeIn {
  from { opacity: 0; }
  to { opacity: 1; }
}

/* 响应式设计 */
@media (max-width: 768px) {
  .result-container {
    padding: var(--spacing-6);
  }
  
  .result-stats {
    grid-template-columns: 1fr;
  }
  
  .result-actions {
    flex-direction: column;
    gap: var(--spacing-3);
  }
  
  .action-button {
    width: 100%;
    justify-content: center;
  }
}

@media (max-width: 640px) {
  .game-result {
    padding: var(--spacing-2);
  }
  
  .result-container {
    padding: var(--spacing-4);
  }
  
  .result-icon {
    font-size: 4rem;
  }
  
  .result-title {
    font-size: var(--text-2xl);
  }
  
  .result-message {
    font-size: var(--text-base);
  }
}
</style>