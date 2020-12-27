<template>
    <div class="snakeOuter">
        <div class="square" :key="square.id" v-for="square in board">
            <div class="squareInner">
                <img class="appleImg" v-if="square.apple" src="../assets/apple.png">
                <div class="snakeSquare" v-if="square.snake"></div>
            </div>
        </div>
        <div class="infoBar">
            Time Elapsed: {{time_elapsed / 1000}}<br>
            Direction: {{direction}}<br>
            Score: {{game_score}}<br>
            <p style="color: red;" v-if="lost">You have lost</p>
        </div>
    </div>
</template>

<script>
export default {
    data() {
        return {
            lost: false,
            direction: 1,
            // direction 0 = up
            // direction 1 = right
            // direction 2 = down
            // direction 3 = left
            time_elapsed: 0,
            tick_speed: 100, // 1 second in milliseconds
            board: [

            ],
            snake: [

            ],
            has_apple: false,
            game_score: 0
        }
    },
    mounted() {
        this.generateBoard()

        setInterval(() => {
            this.onTick()
        }, this.tick_speed)

        window.addEventListener('keydown',this.checkKey);
    },
    methods: {
        generateBoard() {
            var rows = []
            var id = 1
            // for row
            for(var i = 1; i < 20; i++) {
                // for col
                for(var n = 0; n < 20; n++) {
                    if(id === 209) {
                        var squareObj = { id: id++, value: [ i, n ], snake: true, apple: false }
                        this.snake.push([ i, n ]) 
                    } else {
                        var squareObj = { id: id++, value: [ i, n ], snake: false, apple: false }
                    }
                    this.board.push(squareObj)
                }
            }
        },
        generateapple() {
            if(!this.has_apple) {
                var newLoadtionId = Math.floor(Math.random() * (this.board.length - 1 + 1) + 1)
                var boardSquareIndex = this.board.findIndex(x => x.id === newLoadtionId)

                if(this.board[boardSquareIndex].snake) {
                    this.generateapple()
                } else {
                    this.has_apple = true
                    this.board[boardSquareIndex].apple = true
                }
            }
        },
        checkKey(e) {
            if (e.keyCode == '38') {
                // up arrow
                if(this.snake.length > 1) {
                    if(this.direction != 2) {
                        this.direction = 0
                    }
                } else {
                    this.direction = 0
                }
            }
            else if (e.keyCode == '40') {
                // down arrow
                if(this.snake.length > 1) {
                    if(this.direction != 0) {
                        this.direction = 2
                    }
                } else {
                    this.direction = 2
                }
            }
            else if (e.keyCode == '37') {
                // left arrow 
                if(this.snake.length > 1) {
                    if(this.direction != 1) {
                        this.direction = 3
                    }
                } else {
                    this.direction = 3
                }
            }
            else if (e.keyCode == '39') {
                // right arrow
                if(this.snake.length > 1) {
                    if(this.direction != 3) {
                        this.direction = 1
                    }
                } else {
                    this.direction = 1
                }
            }
        },

        // Per 1s
        onTick() {
            if(!this.lost) {
                this.time_elapsed = this.time_elapsed + this.tick_speed
                this.checkCollision() // Check whether you have hit yourself
                this.generateapple() // Generate new apple
                this.addapple()
                if(!this.lost) {
                    this.handleSnakeMovement() // Set movement info to each snake block
                    this.updateBoard() // updates the board
                }
            }
        },
        checkCollision() {
            if(this.snake.length > 1) {
                for(var i = 0; i < this.snake.length; i++) {
                    var squareToCheck = this.snake[i]
                    var index = this.snake.indexOf(squareToCheck)
                    for(var n = 0; n < this.snake.length; n++) {
                        if(JSON.stringify(this.snake[n]) === JSON.stringify(squareToCheck)) {
                            var index2 = this.snake.indexOf(this.snake[n])
                            if(index != index2) {
                                this.lost = true
                            }
                        }
                    }  
                }
            }
        },
        addapple() {
            var snakeAndapple = this.board.find(o => o.snake === true  && o.apple === true);
            if(snakeAndapple) {
                var squareIndex = this.board.indexOf(snakeAndapple)
                this.board[squareIndex].apple = false
                this.has_apple = false
                this.game_score = this.game_score + 1
                this.increaseSnakeLength()
            }
        },
        increaseSnakeLength() {
            var rowPos = this.snake[this.snake.length - 1][0]
            var colPos = this.snake[this.snake.length - 1][1]

            if(this.direction === 0) { // top 
                if(this.rowPos + 1 === 20) {
                    var i = this.rowPos = 1
                    var n = this.colPos
                } else {
                    var i = this.rowPos + 1
                    var n = this.colPos
                }
            } else if (this.direction === 1) { // right 
                if(this.colPos - 1 === -1) {
                    var i = this.rowPos = 0
                    var n = this.colPos
                } else {
                    var i = this.rowPos - 1
                    var n = this.colPos
                }
            } else if (this.direction === 2) { // bottom 
                if(this.rowPos - 1 === 0) {
                    var i = this.rowPos = 19
                    var n = this.colPos
                } else {
                    var i = this.rowPos - 1
                    var n = this.colPos
                }
            } else if (this.direction === 3) { // left 
                if(this.colPos + 1 === 20) {
                    var i = this.rowPos = 0
                    var n = this.colPos
                } else {
                    var i = this.rowPos + 1
                    var n = this.colPos
                }
            }
            this.snake.push([i,n])
        },
        handleSnakeMovement() {

            if(this.direction === 0) { // top 
                
                // Handles addition and removal
                var i = this.snake[0][0] - 1
                var n = this.snake[0][1]
                var frontSnake = [ i, n ]
                this.snake.unshift(frontSnake)
                this.snake.splice(-1,1)

                // Handles body going off grid
                for(var n = 0; n < this.snake.length; n++) {
                    if(this.snake[n][0] - 1 === -1) {
                        this.snake[n][0] = 19
                    }
                }

            } else if (this.direction === 1) { // right 
                var i = this.snake[0][0]
                var n = this.snake[0][1] + 1
                var frontSnake = [ i, n ]
                this.snake.unshift(frontSnake)
                this.snake.splice(-1,1)

                // Handles body going off grid
                for(var n = 0; n < this.snake.length; n++) {
                    if(this.snake[n][1] + 1 === 21) {
                        this.snake[n][1] = 0
                    }
                }

            } else if (this.direction === 2) { // bottom 
                var i = this.snake[0][0] + 1
                var n = this.snake[0][1]
                var frontSnake = [ i, n ]
                this.snake.unshift(frontSnake)
                this.snake.splice(-1,1)

                // Handles body going off grid
                for(var n = 0; n < this.snake.length; n++) {
                    if(this.snake[n][0] + 1 === 21) {
                        this.snake[n][0] = 1
                    }
                }

            } else if (this.direction === 3) { // left 
                var i = this.snake[0][0] 
                var n = this.snake[0][1] - 1
                var frontSnake = [ i, n ]
                this.snake.unshift(frontSnake)
                this.snake.splice(-1,1)

                // Handles body going off grid
                for(var n = 0; n < this.snake.length; n++) {
                    if(this.snake[n][1] - 1 === -2) {
                        this.snake[n][1] = 19
                    }
                }
            }

        },
        updateBoard() {
            // wipe current board snake values
            for(var n = 0; n < this.board.length; n++) {
                this.board[n].snake = false
            }
            // Update board with snake body values
            for(var i = 0; i < this.snake.length; i++) {
                var currentValIndex = this.board.findIndex(x => x.value[0] === this.snake[i][0] && x.value[1] === this.snake[i][1])

                this.board[currentValIndex].snake = true
                
            }
        },


    }
}
</script>

<style scoped>
.snakeOuter {
    width: 80%;
    max-width: 600px;
    background-color: #FFF;
    margin: 0 auto;
    display: flex;
    flex-wrap: wrap;
}

.square {
    width: 5%;
    background-color: black;
    border: 1px solid #313131;
    color: #FFF;
    font-size: 8px;
    position: relative;
}
.square:after {
  content: "";
  display: block;
  padding-bottom: 100%;

}
.squareInner {
    position: absolute;
    top: 0;
    bottom: 0;
    right: 0;
    left: 0;
    display: flex;
    justify-content: center;
    align-items: center;
}
.appleImg {
    width: 90%;
    object-fit: cover;
}
.snakeSquare {
    position: absolute;
    top: 0;
    bottom: 0;
    right: 0;
    left: 0;
    width: 100%;
    height: 100%;
    background-color: #1BFF51;
}
</style>