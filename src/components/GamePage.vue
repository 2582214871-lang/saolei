<template>
  <div class="game-page">
    <GameBoard
      :board="board"
      :mine-count="mineCount"
      :remaining-flags="remainingFlags"
      :time="gameTime"
      :game-over="gameOver"
      :game-won="gameWon"
      :difficulty="difficulty"
      @cell-click="handleCellClick"
      @right-click="handleRightClick"
      @restart="restartGame"
      @go-home="goHome"
    />
  </div>
</template>

<script>
import GameBoard from './GameBoard.vue';

export default {
  name: 'GamePage',
  components: {
    GameBoard,
  },
  props: {
    difficulty: {
      type: String,
      default: 'easy',
    },
  },
  data() {
    return {
      board: [],
      mineCount: 10,
      remainingFlags: 10,
      gameTime: 0,
      gameOver: false,
      gameWon: false,
      gameStarted: false,
      timerInterval: null,
      rows: 9,
      cols: 9,
      mines: 10,
    };
  },
  created() {
    this.setupDifficulty();
    this.initializeBoard();
  },
  beforeUnmount() {
    this.clearTimer();
  },
  methods: {
    setupDifficulty() {
      switch (this.difficulty) {
        case 'easy':
          this.rows = 9;
          this.cols = 9;
          this.mines = 10;
          break;
        case 'medium':
          this.rows = 16;
          this.cols = 16;
          this.mines = 40;
          break;
        case 'hard':
          this.rows = 16;
          this.cols = 30;
          this.mines = 99;
          break;
        default:
          this.rows = 9;
          this.cols = 9;
          this.mines = 10;
      }

      this.mineCount = this.mines;
      this.remainingFlags = this.mines;
    },

    initializeBoard() {
      // 创建空棋盘
      this.board = Array(this.rows * this.cols)
        .fill()
        .map(() => ({
          isMine: false,
          isRevealed: false,
          isFlagged: false,
          adjacentMines: 0,
        }));

      // 随机放置地雷
      let minesPlaced = 0;
      while (minesPlaced < this.mines) {
        const randomIndex = Math.floor(Math.random() * this.board.length);
        if (!this.board[randomIndex].isMine) {
          this.board[randomIndex].isMine = true;
          minesPlaced++;
        }
      }

      // 计算每个格子周围的地雷数量
      this.board.forEach((cell, index) => {
        if (!cell.isMine) {
          cell.adjacentMines = this.countAdjacentMines(index);
        }
      });
    },

    countAdjacentMines(index) {
      const row = Math.floor(index / this.cols);
      const col = index % this.cols;
      let count = 0;

      // 检查周围的8个格子
      for (let r = -1; r <= 1; r++) {
        for (let c = -1; c <= 1; c++) {
          if (r === 0 && c === 0) continue; // 跳过自己

          const newRow = row + r;
          const newCol = col + c;

          // 检查是否在棋盘范围内
          if (
            newRow >= 0 &&
            newRow < this.rows &&
            newCol >= 0 &&
            newCol < this.cols
          ) {
            const adjacentIndex = newRow * this.cols + newCol;
            if (this.board[adjacentIndex].isMine) {
              count++;
            }
          }
        }
      }

      return count;
    },

    handleCellClick(index) {
      if (this.gameOver || this.gameWon) return;
      if (this.board[index].isRevealed || this.board[index].isFlagged) return;

      // 如果是第一次点击，开始游戏
      if (!this.gameStarted) {
        this.gameStarted = true;
        this.startTimer();
      }

      this.revealCell(index);
    },

    handleRightClick(index) {
      if (this.gameOver || this.gameWon) return;
      if (this.board[index].isRevealed) return;

      this.board[index].isFlagged = !this.board[index].isFlagged;

      // 更新剩余标记数
      if (this.board[index].isFlagged) {
        this.remainingFlags--;
      } else {
        this.remainingFlags++;
      }
    },

    revealCell(index) {
      if (this.board[index].isRevealed || this.board[index].isFlagged) return;

      this.board[index].isRevealed = true;

      // 如果点到地雷，游戏结束
      if (this.board[index].isMine) {
        this.endGame(false);
        return;
      }

      // 如果周围没有地雷，自动展开
      if (this.board[index].adjacentMines === 0) {
        this.revealAdjacentCells(index);
      }

      // 检查是否获胜
      this.checkWinCondition();
    },

    revealAdjacentCells(index) {
      const row = Math.floor(index / this.cols);
      const col = index % this.cols;

      // 递归展开周围的格子
      for (let r = -1; r <= 1; r++) {
        for (let c = -1; c <= 1; c++) {
          if (r === 0 && c === 0) continue;

          const newRow = row + r;
          const newCol = col + c;

          if (
            newRow >= 0 &&
            newRow < this.rows &&
            newCol >= 0 &&
            newCol < this.cols
          ) {
            const adjacentIndex = newRow * this.cols + newCol;
            if (
              !this.board[adjacentIndex].isRevealed &&
              !this.board[adjacentIndex].isFlagged
            ) {
              this.revealCell(adjacentIndex);
            }
          }
        }
      }
    },

    checkWinCondition() {
      const allNonMineCellsRevealed = this.board.every((cell) => {
        return cell.isMine || cell.isRevealed;
      });

      if (allNonMineCellsRevealed) {
        this.endGame(true);
      }
    },

    endGame(isWin) {
      this.gameOver = !isWin;
      this.gameWon = isWin;
      this.clearTimer();

      // 如果游戏失败，显示所有地雷
      if (!isWin) {
        this.board.forEach((cell) => {
          if (cell.isMine) {
            cell.isRevealed = true;
          }
        });
      }

      // 发送游戏结果给父组件
      this.$emit(isWin ? 'game-won' : 'game-over', {
        time: this.gameTime,
        flaggedCount: this.mines - this.remainingFlags,
      });
    },

    startTimer() {
      this.timerInterval = setInterval(() => {
        this.gameTime++;
      }, 1000);
    },

    clearTimer() {
      if (this.timerInterval) {
        clearInterval(this.timerInterval);
        this.timerInterval = null;
      }
    },

    restartGame() {
      this.clearTimer();
      this.gameTime = 0;
      this.gameOver = false;
      this.gameWon = false;
      this.gameStarted = false;
      this.remainingFlags = this.mines;
      this.initializeBoard();
    },

    goHome() {
      this.$emit('go-home');
    },
  },
};
</script>

<style scoped>
.game-page {
  width: 100%;
  height: 100vh;
  display: flex;
  justify-content: center;
  align-items: center;
}
</style>
