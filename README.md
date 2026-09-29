```html
<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Car Racing Game</title>

<style>
* {
    box-sizing: border-box;
    margin: 0;
    padding: 0;
}

body {
    background: #111;
    font-family: Arial, sans-serif;
    overflow: hidden;
    text-align: center;
}

h1 {
    color: white;
    margin: 10px;
}

#score {
    color: #00ff66;
    font-size: 24px;
    margin-bottom: 10px;
}

#game {
    width: 360px;
    height: 600px;
    background: #333;
    margin: auto;
    position: relative;
    overflow: hidden;
    border: 5px solid white;
}

/* Road Lines */
.line {
    width: 10px;
    height: 80px;
    background: white;
    position: absolute;
    left: 175px;
}

#player {
    width: 50px;
    height: 90px;
    background: red;
    position: absolute;
    bottom: 20px;
    left: 155px;
    border-radius: 10px;
    border: 3px solid white;
}

.enemy {
    width: 50px;
    height: 90px;
    position: absolute;
    top: -100px;
    border-radius: 10px;
    border: 3px solid white;
}

#gameOver {
    display: none;
    position: absolute;
    top: 200px;
    width: 100%;
    color: white;
    background: rgba(0,0,0,0.85);
    padding: 30px 10px;
    font-size: 30px;
    z-index: 10;
}

button {
    padding: 12px 25px;
    margin-top: 15px;
    font-size: 18px;
    cursor: pointer;
    border: none;
    border-radius: 8px;
}

.controls {
    margin-top: 10px;
}

.controls button {
    width: 80px;
    margin: 5px;
    background: #00aaff;
    color: white;
    font-weight: bold;
}
</style>
</head>

<body>

<h1>🏎️ Car Racing Game</h1>
<div id="score">Score: 0</div>

<div id="game">

    <div class="line" style="top:0px;"></div>
    <div class="line" style="top:150px;"></div>
    <div class="line" style="top:300px;"></div>
    <div class="line" style="top:450px;"></div>

    <div id="player"></div>

    <div id="gameOver">
        💥 GAME OVER 💥
        <br>
        <button onclick="restartGame()">Restart</button>
    </div>

</div>

<div class="controls">
    <button onclick="moveLeft()">⬅ Left</button>
    <button onclick="moveRight()">Right ➡</button>
</div>

<script>

const game = document.getElementById("game");
const player = document.getElementById("player");
const scoreText = document.getElementById("score");
const gameOverScreen = document.getElementById("gameOver");

let playerX = 155;
let score = 0;
let speed = 5;
let gameRunning = true;
let enemies = [];


// Keyboard Controls
document.addEventListener("keydown", function(e) {

    if (e.key === "ArrowLeft") {
        moveLeft();
    }

    if (e.key === "ArrowRight") {
        moveRight();
    }

});


// Move Player Left
function moveLeft() {

    if (!gameRunning) return;

    playerX -= 30;

    if (playerX < 0) {
        playerX = 0;
    }

    player.style.left = playerX + "px";
}


// Move Player Right
function moveRight() {

    if (!gameRunning) return;

    playerX += 30;

    if (playerX > 305) {
        playerX = 305;
    }

    player.style.left = playerX + "px";
}


// Create Enemy
function createEnemy() {

    if (!gameRunning) return;

    const enemy = document.createElement("div");

    enemy.classList.add("enemy");

    const colors = [
        "blue",
        "yellow",
        "green",
        "purple",
        "orange"
    ];

    enemy.style.background =
        colors[Math.floor(Math.random() * colors.length)];

    let x = Math.floor(Math.random() * 310);

    enemy.style.left = x + "px";
    enemy.style.top = "-100px";

    game.appendChild(enemy);

    enemies.push(enemy);
}


// Collision Detection
function collision(a, b) {

    const r1 = a.getBoundingClientRect();
    const r2 = b.getBoundingClientRect();

    return !(
        r1.bottom < r2.top ||
        r1.top > r2.bottom ||
        r1.right < r2.left ||
        r1.left > r2.right
    );
}


// Game Loop
function gameLoop() {

    if (!gameRunning) return;

    // Move road lines
    const lines = document.querySelectorAll(".line");

    lines.forEach(line => {

        let top = parseInt(line.style.top);

        top += speed;

        if (top > 600) {
            top = -80;
        }

        line.style.top = top + "px";

    });


    // Move enemies
    enemies.forEach((enemy, index) => {

        let top = parseInt(enemy.style.top);

        top += speed;

        enemy.style.top = top + "px";


        // Collision
        if (collision(player, enemy)) {

            gameRunning = false;
            gameOverScreen.style.display = "block";

        }


        // Enemy passed player
        if (top > 600) {

            enemy.remove();
            enemies.splice(index, 1);

            score++;

            scoreText.innerText =
                "Score: " + score;

            // Increase difficulty
            if (score % 5 === 0) {
                speed += 1;
            }
        }

    });


    requestAnimationFrame(gameLoop);
}


// Create enemy every 1 second
setInterval(function() {

    if (gameRunning) {
        createEnemy();
    }

}, 1000);


// Restart Game
function restartGame() {

    enemies.forEach(enemy => enemy.remove());

    enemies = [];

    score = 0;
    speed = 5;
    playerX = 155;

    player.style.left = playerX + "px";

    scoreText.innerText = "Score: 0";

    gameOverScreen.style.display = "none";

    gameRunning = true;

    gameLoop();
}


// Start Game
gameLoop();

</script>

</body>
</html>
```
