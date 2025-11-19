<template>
  <div class="home-page">
    <div class="container">
      <header class="header animate-fade-in">
        <div class="logo">
          <h1 class="game-title">💣 扫雷游戏</h1>
          <p class="tagline">经典益智游戏，挑战你的逻辑思维</p>
        </div>
      </header>
      
      <main class="main-content">
        <section class="game-rules animate-fade-in">
          <div class="rules-card card">
            <h2 class="rules-title">游戏规则</h2>
            <ul class="rules-list">
              <li class="rule-item">
                <span class="rule-icon">👆</span>
                <span class="rule-text">左键点击揭开格子</span>
              </li>
              <li class="rule-item">
                <span class="rule-icon">🚩</span>
                <span class="rule-text">右键标记地雷位置</span>
              </li>
              <li class="rule-item">
                <span class="rule-icon">🔢</span>
                <span class="rule-text">数字表示周围8个格子中的地雷数量</span>
              </li>
              <li class="rule-item">
                <span class="rule-icon">🏁</span>
                <span class="rule-text">标记所有地雷或揭开所有安全格子即可获胜</span>
              </li>
            </ul>
          </div>
        </section>
        
        <section class="difficulty-selection animate-fade-in">
          <div class="difficulty-card card">
            <h2 class="difficulty-title">选择难度</h2>
            <div class="difficulty-options">
              <div 
                class="difficulty-option" 
                :class="{ active: selectedDifficulty === 'easy' }"
                @click="selectDifficulty('easy')"
              >
                <div class="difficulty-header">
                  <h3>初级</h3>
                  <span class="difficulty-size">9×9</span>
                </div>
                <div class="difficulty-details">
                  <span class="mine-count">10个地雷</span>
                  <div class="difficulty-stars">
                    <span class="star">⭐</span>
                  </div>
                </div>
              </div>
              
              <div 
                class="difficulty-option" 
                :class="{ active: selectedDifficulty === 'medium' }"
                @click="selectDifficulty('medium')"
              >
                <div class="difficulty-header">
                  <h3>中级</h3>
                  <span class="difficulty-size">16×16</span>
                </div>
                <div class="difficulty-details">
                  <span class="mine-count">40个地雷</span>
                  <div class="difficulty-stars">
                    <span class="star">⭐</span>
                    <span class="star">⭐</span>
                  </div>
                </div>
              </div>
              
              <div 
                class="difficulty-option" 
                :class="{ active: selectedDifficulty === 'hard' }"
                @click="selectDifficulty('hard')"
              >
                <div class="difficulty-header">
                  <h3>高级</h3>
                  <span class="difficulty-size">16×30</span>
                </div>
                <div class="difficulty-details">
                  <span class="mine-count">99个地雷</span>
                  <div class="difficulty-stars">
                    <span class="star">⭐</span>
                    <span class="star">⭐</span>
                    <span class="star">⭐</span>
                  </div>
                </div>
              </div>
            </div>
            
            <button class="start-button" @click="startGame">
              <span class="button-icon">🎮</span>
              开始游戏
            </button>
          </div>
        </section>
      </main>
      
      <footer class="footer">
        <p class="footer-text">© 2023 扫雷游戏 | 使用 Vue 3 构建</p>
      </footer>
    </div>
  </div>
</template>

<script>
export default {
  name: 'HomePage',
  data() {
    return {
      selectedDifficulty: 'easy'
    }
  },
  methods: {
    selectDifficulty(level) {
      this.selectedDifficulty = level;
    },
    startGame() {
      this.$emit('start-game', this.selectedDifficulty);
    }
  }
}
</script>

<style scoped>
.home-page {
  min-height: 100vh;
  background: linear-gradient(135deg, var(--gray-50) 0%, var(--primary-light) 100%);
  padding: var(--spacing-6) 0;
}

.container {
  max-width: 1200px;
  margin: 0 auto;
  padding: 0 var(--spacing-4);
}

.header {
  text-align: center;
  margin-bottom: var(--spacing-12);
}

.logo {
  display: flex;
  flex-direction: column;
  align-items: center;
  gap: var(--spacing-2);
}

.game-title {
  font-size: var(--text-5xl);
  font-weight: 700;
  color: var(--primary-color);
  margin: 0;
  text-shadow: 2px 2px 4px rgba(0, 0, 0, 0.1);
}

.tagline {
  font-size: var(--text-lg);
  color: var(--gray-600);
  margin: 0;
  max-width: 600px;
}

.main-content {
  display: grid;
  grid-template-columns: 1fr;
  gap: var(--spacing-8);
  margin-bottom: var(--spacing-12);
}

.game-rules {
  animation-delay: 0.1s;
}

.difficulty-selection {
  animation-delay: 0.2s;
}

.rules-card, .difficulty-card {
  padding: var(--spacing-8);
  border-radius: var(--radius-2xl);
  box-shadow: var(--shadow-lg);
  border: 1px solid var(--gray-200);
  transition: transform var(--transition-normal), box-shadow var(--transition-normal);
}

.rules-card:hover, .difficulty-card:hover {
  transform: translateY(-5px);
  box-shadow: var(--shadow-xl);
}

.rules-title, .difficulty-title {
  font-size: var(--text-2xl);
  font-weight: 600;
  color: var(--gray-800);
  margin-bottom: var(--spacing-4);
  text-align: center;
}

.rules-list {
  list-style: none;
  padding: 0;
}

.rule-item {
  display: flex;
  align-items: center;
  gap: var(--spacing-3);
  margin-bottom: var(--spacing-4);
  padding: var(--spacing-3);
  background-color: var(--gray-50);
  border-radius: var(--radius-lg);
  transition: background-color var(--transition-fast);
}

.rule-item:hover {
  background-color: var(--primary-light);
}

.rule-icon {
  font-size: var(--text-xl);
}

.rule-text {
  font-size: var(--text-base);
  color: var(--gray-700);
}

.difficulty-options {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(200px, 1fr));
  gap: var(--spacing-6);
  margin-bottom: var(--spacing-8);
}

.difficulty-option {
  padding: var(--spacing-5);
  background-color: var(--gray-50);
  border: 2px solid var(--gray-200);
  border-radius: var(--radius-xl);
  cursor: pointer;
  transition: all var(--transition-normal);
  text-align: center;
}

.difficulty-option:hover {
  border-color: var(--primary-color);
  transform: translateY(-3px);
  box-shadow: var(--shadow-md);
}

.difficulty-option.active {
  border-color: var(--primary-color);
  background-color: var(--primary-light);
  box-shadow: var(--shadow-md);
}

.difficulty-header {
  margin-bottom: var(--spacing-3);
}

.difficulty-header h3 {
  font-size: var(--text-xl);
  font-weight: 600;
  color: var(--gray-800);
  margin: 0;
}

.difficulty-size {
  font-size: var(--text-base);
  color: var(--gray-600);
  display: block;
  margin-top: var(--spacing-1);
}

.difficulty-details {
  display: flex;
  flex-direction: column;
  align-items: center;
  gap: var(--spacing-2);
}

.mine-count {
  font-size: var(--text-sm);
  color: var(--gray-600);
}

.difficulty-stars {
  display: flex;
  gap: var(--spacing-1);
}

.star {
  font-size: var(--text-base);
}

.start-button {
  width: 100%;
  padding: var(--spacing-4);
  font-size: var(--text-lg);
  font-weight: 600;
  background: linear-gradient(135deg, var(--primary-color) 0%, var(--primary-hover) 100%);
  color: var(--white);
  border: none;
  border-radius: var(--radius-xl);
  cursor: pointer;
  transition: all var(--transition-normal);
  display: flex;
  align-items: center;
  justify-content: center;
  gap: var(--spacing-2);
  box-shadow: var(--shadow-md);
}

.start-button:hover {
  transform: translateY(-2px);
  box-shadow: var(--shadow-lg);
}

.button-icon {
  font-size: var(--text-xl);
}

.footer {
  text-align: center;
  padding: var(--spacing-6) 0;
  border-top: 1px solid var(--gray-200);
}

.footer-text {
  font-size: var(--text-sm);
  color: var(--gray-500);
  margin: 0;
}

/* 响应式设计 */
@media (max-width: 768px) {
  .game-title {
    font-size: var(--text-4xl);
  }
  
  .tagline {
    font-size: var(--text-base);
  }
  
  .main-content {
    gap: var(--spacing-6);
  }
  
  .rules-card, .difficulty-card {
    padding: var(--spacing-6);
  }
  
  .difficulty-options {
    grid-template-columns: 1fr;
    gap: var(--spacing-4);
  }
}

@media (max-width: 640px) {
  .home-page {
    padding: var(--spacing-4) 0;
  }
  
  .header {
    margin-bottom: var(--spacing-8);
  }
  
  .game-title {
    font-size: var(--text-3xl);
  }
  
  .rules-card, .difficulty-card {
    padding: var(--spacing-4);
  }
  
  .rule-item {
    padding: var(--spacing-2);
  }
  
  .difficulty-option {
    padding: var(--spacing-4);
  }
}
</style>