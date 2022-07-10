<template>
<div>
     <canvas ref="board" id="game-canvas" :width="boardSizePx" :height="boardSizePx"></canvas>
</div>
</template>
<script>
import constants from './constants'

export default {
     name: 'MainSnakeWindow',
     props: {
          cellSize: Number,
          boardSize: Number,
          speed: Number,
          isPlaying: Boolean,
          stop: Function,
          addScores: Function,
          scores: Number
     },
     watch: {
          isPlaying(value) {
               if(value) {
                    this.resetSnake()
                    this.move()
               }
          }
     },
     methods: {
          resetSnake () {
               // Game always starts from the middle
               this.snake = [
                    {
                         x: this.getMiddleCell(),
                         y: this.getMiddleCell()
                    }
               ]
               this.direction = constants[0]
          },
          getMiddleCell () {
               return Math.round(this.boardSize/2)
          },
          move () {
               if(!this.isPlaying){
                    return
               }
               this.clear()
               this.setTargetCell()
               // On every move a new head is created and put in the front of the snake
               const newHeadCell = {
                    x: this.snake[0].x + this.direction.move.x,
                    y: this.snake[0].y + this.direction.move.y
               }

               if(this.isCellOutOfBoard(newHeadCell)) {
                    this.stop()
                    alert('Game is over! Your score is ' + this.scores + ' points')
               }
               
               // When the target cell is eaten it is added to the front of the snake array
               if(this.isTargetNewHead()) {
                    this.snake.unshift(this.targetCell)
                    this.targetCell = null
                    this.addScores(this.speed)

               } else {
                    this.snake.unshift(newHeadCell)
                    // The last element of the snake is popped on every move
                    this.snake.pop()
               }

               this.boardContext.beginPath() //Canvas exclusive stuff
               this.snake.forEach(this.drawCell) // Every cell is redrawed
               this.boardContext.closePath() //Canvas exclusive stuff

               setTimeout(this.move, this.getMoveDelay())
          },
          clear () {
               this.boardContext.clearRect(0, 0, this.boardSizePx, this.boardSizePx)
          },
          drawCell ({ x,y }) {
               this.boardContext.rect(
                    x * this.cellSize,
                    y * this.cellSize,
                    this.cellSize,
                    this.cellSize
               )
               this.boardContext.fillStyle = "black"
               this.boardContext.fill()
          },
          getMoveDelay() {
               return (2 / Number(this.speed)) * 1000;
          },
          isCellOutOfBoard ({x, y}) {
               return x < 0 || y < 0 || x>=this.boardSize || y>=this.boardSize
          },
          onKeyPress (event) {
               const newDirection = constants.find(c => c.keyCode === event.keyCode)

               if(!newDirection) {
                    return
               }

               // Player cant move in the oposite direction (from up to down, from right to left and vice versa)
               if(Math.abs(newDirection.keyCode - this.direction.keyCode) !== 2) {
                    this.direction = newDirection
               }
          },
          setTargetCell () {
               if(!this.targetCell) {
                    let targetCell = this.getRandomCell()
                    while (this.cellsAmountInSnake(targetCell) > 0) {
                         targetCell = this.getRandomCell()
                    }
                    this.targetCell = targetCell
               }

               this.boardContext.beginPath()
               this. boardContext.rect(
                    this.targetCell.x * this.cellSize,
                    this.targetCell.y * this.cellSize,
                    this.cellSize,
                    this.cellSize
               )
               this.boardContext.fillStyle = "red"
               this.boardContext.fill()
               this.boardContext.closePath()
          },
          getRandomCell () {
               return {
                    x: Math.floor(Math.random() * this.boardSize),
                    y: Math.floor(Math.random() * this.boardSize)
               }
          },
          cellsAmountInSnake (cell) {
               return this.snake.filter((x, y) => x === cell.x && y === cell.y)
          },
          isTargetNewHead () {
               return (
                    this.snake[0].x + this.direction.move.x === this.targetCell.x &&
                    this.snake[0].y + this.direction.move.y === this.targetCell.y
               )
          }
     },
     computed: {
          boardSizePx () {
               return this.cellSize * this.boardSize
          }
     },
     mounted () {
          this.boardContext = this.$refs.board.getContext("2d")
          window.addEventListener('keydown', this.onKeyPress)
     },
     created () {
          this.resetSnake()
     },
     beforeDestroy () {
          window.removeEventListener('keydown', this.onKeyPress)
     }
}
</script>
<style scoped src="./MainSnakeWindow.css">
</style>