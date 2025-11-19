<template>
  <div class="game-controls">
    <div class="game-info">
      <div class="info-item">
        <span class="label">地雷数:</span>
        <span class="value">{{ mines }}</span>
      </div>
      <div class="info-item">
        <span class="label">剩余标记:</span>
        <span class="value">{{ remainingFlags }}</span>
      </div>
      <div class="info-item">
        <span class="label">时间:</span>
        <span class="value">{{ formattedTime }}</span>
      </div>
    </div>
    
    <div class="game-status">
      <div v-if="gameStatus === 'playing'" class="status playing">
        游戏进行中...
      </div>
      <div v-else-if="gameStatus === 'won'" class="status won">
        🎉 恭喜你赢了！
      </div>
      <div v-else-if="gameStatus === 'lost'" class="status lost">
        💥 游戏结束！
      </div>
    </div>
    
    <div class="difficulty-selector">
      <span class="difficulty-label">难度:</span>
      <span class="difficulty-value">{{ getDifficultyText() }}</span>
    </div>
    
    <button class="reset-button" @click="resetGame">
      重新开始
    </button>
  </div>
</template>

<script setup>
import { ref, computed, watch } from 'vue'

const props = defineProps({
  mines: {
    type: Number,
    required: true
  },
  flaggedCount: {
    type: Number,
    required: true
  },
  gameStatus: {
    type: String,
    required: true
  },
  difficulty: {
    type: String,
    default: 'beginner'
  }
})

const emit = defineEmits(['reset', 'difficultyChange'])

const selectedDifficulty = ref('beginner')
const elapsedTime = ref(0)
let timer = null
const gameStarted = ref(false)

const difficulties = {
  beginner: { rows: 9, cols: 9, mines: 10 },
  intermediate: { rows: 16, cols: 16, mines: 40 },
  expert: { rows: 16, cols: 30, mines: 99 }
}

const remainingFlags = computed(() => {
  return Math.max(0, props.mines - props.flaggedCount)
})

const formattedTime = computed(() => {
  const minutes = Math.floor(elapsedTime.value / 60)
  const seconds = elapsedTime.value % 60
  return `${minutes.toString().padStart(2, '0')}:${seconds.toString().padStart(2, '0')}`
})

const startTimer = () => {
  console.log('startTimer被调用')
  if (timer) clearInterval(timer)
  elapsedTime.value = 0
  gameStarted.value = true
  timer = setInterval(() => {
    elapsedTime.value++
    console.log('计时器更新:', elapsedTime.value)
  }, 1000)
}

const stopTimer = () => {
  if (timer) {
    clearInterval(timer)
    timer = null
  }
  gameStarted.value = false
}

const getDifficultyText = () => {
  switch (props.difficulty) {
    case 'beginner':
      return '初级 (9×9, 10雷)'
    case 'intermediate':
      return '中级 (16×16, 40雷)'
    case 'expert':
      return '高级 (16×30, 99雷)'
    default:
      return '初级 (9×9, 10雷)'
  }
}

const resetGame = () => {
  console.log('GameControls resetGame被调用')
  stopTimer()
  elapsedTime.value = 0
  gameStarted.value = false
  emit('reset')
  // 不立即启动计时器，等待用户第一次点击
}

// 监听游戏状态变化
watch(() => props.gameStatus, (newStatus) => {
  if (newStatus === 'playing') {
    // 不在这里自动启动计时器，等待GameBoard组件通知
  } else {
    stopTimer()
  }
})

// 初始化时不要启动计时器
// startTimer()

// 暴露方法给父组件
defineExpose({
  startTimer,
  getElapsedTime: () => elapsedTime.value
})
</script>

<style scoped>
.game-controls {
  display: flex;
  flex-direction: column;
  align-items: center;
  gap: 15px;
  margin: 20px 0;
  padding: 15px;
  background-color: #f5f5f5;
  border-radius: 8px;
  box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
}

.game-info {
  display: flex;
  gap: 20px;
}

.info-item {
  display: flex;
  flex-direction: column;
  align-items: center;
}

.label {
  font-size: 14px;
  color: #666;
}

.value {
  font-size: 18px;
  font-weight: bold;
  color: #333;
}

.game-status {
  min-height: 30px;
}

.status {
  font-size: 18px;
  font-weight: bold;
  text-align: center;
}

.playing {
  color: #2196f3;
}

.won {
  color: #4caf50;
}

.lost {
  color: #f44336;
}

.difficulty-selector {
  display: flex;
  align-items: center;
  gap: 10px;
}

.difficulty-label {
  font-size: 14px;
  color: #666;
}

.difficulty-value {
  font-size: 16px;
  font-weight: bold;
  color: #333;
}

.reset-button {
  padding: 8px 16px;
  background-color: #2196f3;
  color: white;
  border: none;
  border-radius: 4px;
  cursor: pointer;
  font-size: 16px;
  transition: background-color 0.2s;
}

.reset-button:hover {
  background-color: #0d8aee;
}
</style>