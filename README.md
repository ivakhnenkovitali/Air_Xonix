<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Air Xonix</title>
    <style>
        body {
            display: flex;
            justify-content: center;
            align-items: center;
            height: 100vh;
            margin: 0;
            background-color: #000;
            color: #fff;
            font-family: Arial, sans-serif;
        }
        canvas {
            border: 1px solid #fff;
            background-color: #000;
        }
    </style>
</head>
<body>
    <canvas id="gameCanvas" width="800" height="600"></canvas>

    <script>
        const canvas = document.getElementById('gameCanvas');
        const ctx = canvas.getContext('2d');

        const player = {
            x: canvas.width / 2,
            y: canvas.height - 30,
            width: 20,
            height: 20,
            speed: 5,
            dx: 0,
            dy: 0
        };

        const enemies = [];
        const enemySpeed = 2;
        const enemySize = 20;

        let trail = [];
        let gameOver = false;

        function drawPlayer() {
            ctx.fillStyle = 'blue';
            ctx.fillRect(player.x, player.y, player.width, player.height);
        }

        function drawEnemies() {
            ctx.fillStyle = 'red';
            enemies.forEach(enemy => {
                ctx.fillRect(enemy.x, enemy.y, enemySize, enemySize);
            });
        }

        function drawTrail() {
            ctx.strokeStyle = 'lime';
            ctx.beginPath();
            ctx.moveTo(trail[0].x, trail[0].y);
            for (let i = 1; i < trail.length; i++) {
                ctx.lineTo(trail[i].x, trail[i].y);
            }
            ctx.stroke();
        }

        function updatePlayer() {
            player.x += player.dx;
            player.y += player.dy;

            if (player.x < 0) player.x = 0;
            if (player.x + player.width > canvas.width) player.x = canvas.width - player.width;
            if (player.y < 0) player.y = 0;
            if (player.y + player.height > canvas.height) player.y = canvas.height - player.height;

            trail.push({ x: player.x, y: player.y });
        }

        function updateEnemies() {
            enemies.forEach(enemy => {
                enemy.x += enemy.dx;
                enemy.y += enemy.dy;

                if (enemy.x < 0 || enemy.x + enemySize > canvas.width) enemy.dx *= -1;
                if (enemy.y < 0 || enemy.y + enemySize > canvas.height) enemy.dy *= -1;
            });
        }

        function checkCollisions() {
            enemies.forEach(enemy => {
                if (player.x < enemy.x + enemySize &&
                    player.x + player.width > enemy.x &&
                    player.y < enemy.y + enemySize &&
                    player.y + player.height > enemy.y) {
                    gameOver = true;
                }
            });
        }

        function gameLoop() {
            if (gameOver) {
                ctx.fillStyle = 'white';
                ctx.font = '30px Arial';
                ctx.fillText('Game Over', canvas.width / 2 - 80, canvas.height / 2);
                return;
            }

            ctx.clearRect(0, 0, canvas.width, canvas.height);

            drawPlayer();
            drawEnemies();
            drawTrail();

            updatePlayer();
            updateEnemies();
            checkCollisions();

            requestAnimationFrame(gameLoop);
        }

        function spawnEnemy() {
            const side = Math.floor(Math.random() * 4);
            let x, y, dx, dy;

            switch (side) {
                case 0: // Top
                    x = Math.random() * canvas.width;
                    y = 0;
                    dx = (Math.random() - 0.5) * enemySpeed;
                    dy = enemySpeed;
                    break;
                case 1: // Bottom
                    x = Math.random() * canvas.width;
                    y = canvas.height - enemySize;
                    dx = (Math.random() - 0.5) * enemySpeed;
                    dy = -enemySpeed;
                    break;
                case 2: // Left
                    x = 0;
                    y = Math.random() * canvas.height;
                    dx = enemySpeed;
                    dy = (Math.random() - 0.5) * enemySpeed;
                    break;
                case 3: // Right
                    x = canvas.width - enemySize;
                    y = Math.random() * canvas.height;
                    dx = -enemySpeed;
                    dy = (Math.random() - 0.5) * enemySpeed;
                    break;
            }

            enemies.push({ x, y, dx, dy });
        }

        document.addEventListener('keydown', (e) => {
            if (e.key === 'ArrowLeft') player.dx = -player.speed;
            if (e.key === 'ArrowRight') player.dx = player.speed;
            if (e.key === 'ArrowUp') player.dy = -player.speed;
            if (e.key === 'ArrowDown') player.dy = player.speed;
        });

        document.addEventListener('keyup', () => {
            player.dx = 0;
            player.dy = 0;
        });

        setInterval(spawnEnemy, 2000);
        gameLoop();
    </script>
</body>
</html>




______________________________________________

body:

display: flex: Используется для центрирования содержимого страницы.

justify-content: center и align-items: center: Центрируют элемент <canvas> по горизонтали и вертикали.

height: 100vh: Занимает всю высоту экрана.

background-color: #000: Черный фон страницы.

color: #fff: Белый цвет текста (если он будет использоваться).

font-family: Arial, sans-serif: Шрифт для текста.

canvas:

border: 1px solid #fff: Белая рамка вокруг игрового поля.

background-color: #000: Черный фон внутри canvas.

_____________________________________________________

Описание:
canvas: Получаем элемент <canvas>.

ctx: Контекст для рисования на canvas.

player: Объект, представляющий игрока. Содержит:

x, y: Координаты игрока.

width, height: Размеры игрока.

speed: Скорость перемещения.

dx, dy: Изменение координат по осям X и Y (для движения).

enemies: Массив для хранения врагов.

enemySpeed: Скорость врагов.

enemySize: Размер врагов.

trail: Массив для хранения точек следа игрока.

gameOver: Флаг, указывающий, завершена ли игра.

____________________________________________

drawPlayer(): Рисует игрока (синий квадрат).

drawEnemies(): Рисует всех врагов (красные квадраты).

drawTrail(): Рисует след игрока (зеленая линия).



_____________________________

Описание:
updatePlayer(): Обновляет позицию игрока и добавляет текущие координаты в массив trail.

updateEnemies(): Обновляет позиции врагов и меняет направление, если они достигают границы canvas.

checkCollisions(): Проверяет столкновение игрока с врагами. Если столкновение обнаружено, игра завершается.

____________________________________

gameLoop(): Основной цикл игры. Он:

Очищает canvas.

Рисует игрока, врагов и след.

Обновляет позиции объектов.

Проверяет столкновения.

Повторяет цикл с помощью requestAnimationFrame.

_________________________________________

Описание:
spawnEnemy(): Создает нового врага на случайной стороне экрана и добавляет его в массив enemies.


______________________________________

keydown: Обрабатывает нажатие клавиш стрелок и изменяет скорость игрока.

keyup: Останавливает игрока, когда клавиша отпущена.


___________________________

Описание:
setInterval(spawnEnemy, 2000): Каждые 2 секунды создает нового врага.

gameLoop(): Запускает игровой цикл.














