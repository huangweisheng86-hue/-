<!DOCTYPE html>
<html lang="zh">
<head>
    <meta charset="UTF-8">
    <title>接球游戏</title>
    <link rel="stylesheet" href="style.css">
</head>
<body>
    <h1>接球游戏</h1>
    <p>得分：<span id="score">0</span></p>
    <canvas id="game" width="400" height="500"></canvas>
    <p>用 ← → 方向键移动挡板</p>
    <script src="game.js"></script>
</body>
</html>
body {
    display: flex;
    flex-direction: column;
    align-items: center;
    background: #1a1a2e;
    color: white;
    font-family: Arial, sans-serif;
    margin: 0;
    padding: 20px;
}
canvas {
    background: #16213e;
    border: 2px solid #0f3460;
    border-radius: 8px;
}
#score {
    color: #e94560;
    font-weight: bold;
    font-size: 20px;
}
const canvas = document.getElementById('game');
const ctx = canvas.getContext('2d');
const scoreEl = document.getElementById('score');

// 挡板
const paddle = {
    width: 80,
    height: 12,
    x: 160,
    y: 470,
    speed: 7
};

// 球
const ball = {
    x: 200,
    y: 100,
    radius: 8,
    dx: 3,      // 水平速度
    dy: 3       // 垂直速度
};

let score = 0;
let leftPressed = false;
let rightPressed = false;

// 键盘控制
document.addEventListener('keydown', e => {
    if (e.key === 'ArrowLeft') leftPressed = true;
    if (e.key === 'ArrowRight') rightPressed = true;
});
document.addEventListener('keyup', e => {
    if (e.key === 'ArrowLeft') leftPressed = false;
    if (e.key === 'ArrowRight') rightPressed = false;
});

// 更新游戏状态
function update() {
    // 挡板移动
    if (leftPressed && paddle.x > 0) paddle.x -= paddle.speed;
    if (rightPressed && paddle.x < canvas.width - paddle.width) paddle.x += paddle.speed;

    // 球移动
    ball.x += ball.dx;
    ball.y += ball.dy;

    // 撞左右墙
    if (ball.x - ball.radius < 0 || ball.x + ball.radius > canvas.width) {
        ball.dx = -ball.dx;
    }

    // 撞上墙
    if (ball.y - ball.radius < 0) {
        ball.dy = -ball.dy;
    }

    // 撞挡板
    if (
        ball.y + ball.radius > paddle.y &&
        ball.x > paddle.x &&
        ball.x < paddle.x + paddle.width &&
        ball.dy > 0
    ) {
        ball.dy = -ball.dy;
        score++;
        scoreEl.textContent = score;
    }

    // 掉到底部 → 重置
    if (ball.y - ball.radius > canvas.height) {
        ball.x = 200;
        ball.y = 100;
        ball.dx = 3 * (Math.random() > 0.5 ? 1 : -1);
        ball.dy = 3;
        score = 0;
        scoreEl.textContent = score;
    }
}

// 绘制画面
function draw() {
    ctx.clearRect(0, 0, canvas.width, canvas.height);

    // 画挡板
    ctx.fillStyle = '#e94560';
    ctx.fillRect(paddle.x, paddle.y, paddle.width, paddle.height);

    // 画球
    ctx.beginPath();
    ctx.arc(ball.x, ball.y, ball.radius, 0, Math.PI * 2);
    ctx.fillStyle = '#ffffff';
    ctx.fill();
    ctx.closePath();
}

// 游戏主循环
function gameLoop() {
    update();
    draw();
    requestAnimationFrame(gameLoop);
}

gameLoop();
