<template>
  <div v-if="visible" class="modal-overlay" @click="closeModal">
    <div class="modal-content" @click.stop>
      <div class="modal-header">
        <h2>💥 游戏结束！</h2>
      </div>
      <div class="modal-body">
        <p>很遗憾，你踩到了地雷！再试一次吧！</p>
        <div class="game-stats">
          <div class="stat-item">
            <span class="stat-label">难度:</span>
            <span class="stat-value">{{ difficulty }}</span>
          </div>
          <div class="stat-item">
            <span class="stat-label">用时:</span>
            <span class="stat-value">{{ formattedTime }}</span>
          </div>
        </div>
      </div>
      <div class="modal-footer">
        <button class="btn btn-primary" @click="playAgain">再玩一次</button>
        <button class="btn btn-secondary" @click="closeModal">关闭</button>
        <button class="btn btn-result" @click="showResult">查看结果</button>
      </div>
    </div>
  </div>
</template>

<script setup>
import { computed } from 'vue'

const props = defineProps({
  visible: {
    type: Boolean,
    default: false
  },
  difficulty: {
    type: String,
    default: '初级'
  },
  time: {
    type: Number,
    default: 0
  }
})

const emit = defineEmits(['close', 'playAgain', 'showResult'])

const formattedTime = computed(() => {
  const minutes = Math.floor(props.time / 60)
  const seconds = props.time % 60
  return `${minutes.toString().padStart(2, '0')}:${seconds.toString().padStart(2, '0')}`
})

const closeModal = () => {
  emit('close')
}

const playAgain = () => {
  emit('playAgain')
}

const showResult = () => {
  emit('showResult')
}
</script>

<style scoped>
.modal-overlay {
  position: fixed;
  top: 0;
  left: 0;
  right: 0;
  bottom: 0;
  background-color: rgba(0, 0, 0, 0.5);
  display: flex;
  justify-content: center;
  align-items: center;
  z-index: 1000;
}

.modal-content {
  background-color: white;
  border-radius: 8px;
  box-shadow: 0 4px 12px rgba(0, 0, 0, 0.15);
  width: 90%;
  max-width: 400px;
  padding: 0;
  overflow: hidden;
  animation: modalFadeIn 0.3s ease-out;
}

@keyframes modalFadeIn {
  from {
    opacity: 0;
    transform: scale(0.9);
  }
  to {
    opacity: 1;
    transform: scale(1);
  }
}

.modal-header {
  background-color: #f44336;
  color: white;
  padding: 16px 20px;
  text-align: center;
}

.modal-header h2 {
  margin: 0;
  font-size: 24px;
}

.modal-body {
  padding: 20px;
  text-align: center;
}

.modal-body p {
  margin: 0 0 20px 0;
  font-size: 16px;
  color: #333;
}

.game-stats {
  display: flex;
  justify-content: space-around;
  margin-bottom: 10px;
}

.stat-item {
  display: flex;
  flex-direction: column;
  align-items: center;
}

.stat-label {
  font-size: 14px;
  color: #666;
  margin-bottom: 5px;
}

.stat-value {
  font-size: 18px;
  font-weight: bold;
  color: #333;
}

.modal-footer {
  display: flex;
  justify-content: center;
  gap: 10px;
  padding: 15px 20px;
  background-color: #f5f5f5;
}

.btn {
  padding: 8px 16px;
  border: none;
  border-radius: 4px;
  cursor: pointer;
  font-size: 16px;
  transition: background-color 0.2s;
}

.btn-primary {
  background-color: #f44336;
  color: white;
}

.btn-primary:hover {
  background-color: #d32f2f;
}

.btn-secondary {
  background-color: #757575;
  color: white;
}

.btn-secondary:hover {
  background-color: #616161;
}

.btn-result {
  background-color: #2196f3;
  color: white;
}

.btn-result:hover {
  background-color: #0d8aee;
}
</style>