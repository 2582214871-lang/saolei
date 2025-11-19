<template>
  <div id="app">
    <HomePage v-if="currentPage === 'home'" @start-game="startGame" />
    <GamePage 
      v-else-if="currentPage === 'game'" 
      :difficulty="currentDifficulty"
      @go-home="goToHome" 
      @game-over="gameOver" 
      @game-won="gameWon"
    />
    <GameResult 
      v-else-if="currentPage === 'result'" 
      :is-win="isWin"
      :difficulty="currentDifficulty"
      :time="gameTime"
      :mine-count="mineCount"
      :flagged-count="flaggedCount"
      @play-again="playAgain"
      @go-home="goToHome"
    />
  </div>
</template>

<script>
import HomePage from './components/HomePage.vue';
import GamePage from './components/GamePage.vue';
import GameResult from './components/GameResult.vue';

export default {
  name: 'App',
  components: {
    HomePage,
    GamePage,
    GameResult
  },
  data() {
    return {
      currentPage: 'home', // home, game, result
      currentDifficulty: 'easy',
      isWin: false,
      gameTime: 0,
      mineCount: 0,
      flaggedCount: 0
    };
  },
  methods: {
    startGame(difficulty) {
      this.currentDifficulty = difficulty;
      this.currentPage = 'game';
      
      // 根据难度设置地雷数量
      switch (difficulty) {
        case 'easy':
          this.mineCount = 10;
          break;
        case 'medium':
          this.mineCount = 40;
          break;
        case 'hard':
          this.mineCount = 99;
          break;
        default:
          this.mineCount = 10;
      }
      
      this.flaggedCount = 0;
      this.gameTime = 0;
    },
    goToHome() {
      this.currentPage = 'home';
    },
    gameOver(gameData) {
      this.isWin = false;
      this.gameTime = gameData.time;
      this.flaggedCount = gameData.flaggedCount;
      this.currentPage = 'result';
    },
    gameWon(gameData) {
      this.isWin = true;
      this.gameTime = gameData.time;
      this.flaggedCount = gameData.flaggedCount;
      this.currentPage = 'result';
    },
    playAgain() {
      this.currentPage = 'game';
      this.flaggedCount = 0;
      this.gameTime = 0;
    }
  }
}
</script>

<style>
#app {
  font-family: var(--font-family-sans);
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  color: var(--gray-800);
  background-color: var(--gray-50);
  min-height: 100vh;
  overflow-x: hidden;
}

/* 全局响应式设计 */
@media (max-width: 768px) {
  #app {
    font-size: var(--text-sm);
  }
}

@media (max-width: 640px) {
  #app {
    font-size: var(--text-xs);
  }
}
</style>
