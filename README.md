# bunnykny.github.io
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Flappy Adventure & Sky Hopper</title>
    <style>
        body { text-align: center; margin: 0; overflow: hidden; background: skyblue; }
        canvas { display: block; margin: auto; background: url('https://i.imgur.com/3z4foyA.png') repeat-x; }
        #menu { margin-bottom: 10px; position: absolute; top: 10px; left: 50%; transform: translateX(-50%); }
        #intro { position: absolute; top: 0; left: 0; width: 100%; height: 100%; background: white; display: flex; flex-direction: column; justify-content: center; align-items: center; }
    </style>
</head>
<body>
    <div id="intro">
        <h1>Welcome to Flappy Adventure & Sky Hopper</h1>
        <p>Made by KOY BUNNY</p>
        <p>Select an option below:</p>
        <button onclick="closeIntro()">Start</button>
        <button onclick="showInstructions()">How to Play</button>
    </div>
    <div id="menu">
        <label for="difficulty">Select Difficulty:</label>
        <select id="difficulty">
            <option value="easy">Easy</option>
            <option value="hard">Hard</option>
        </select>
        <button onclick="startGame()">Start Game</button>
        <p>Press any key or tap the screen to make the bird jump. Avoid hitting the pipes!</p>
    </div>
    <canvas id="gameCanvas"></canvas>
    <script>
        const canvas = document.getElementById("gameCanvas");
        const ctx = canvas.getContext("2d");
        let bird, pipes, frame, gameOver, gravity, lift, pipeSpeed, pipeGap;
        let backgroundX = 0;
        const backgroundSpeed = 1;
        const backgroundImage = new Image();
        backgroundImage.src = 'https://i.imgur.com/3z4foyA.png';

        // Resize canvas for mobile/PC compatibility
        function resizeCanvas() {
            canvas.width = window.innerWidth * 0.9; // 90% of window width
            canvas.height = window.innerHeight * 0.7; // 70% of window height
        }
        window.addEventListener('resize', resizeCanvas);
        resizeCanvas();

        function closeIntro() {
            document.getElementById("intro").style.display = "none";
        }
        
        function showInstructions() {
            alert("Press any key or tap the screen to make the bird jump. Avoid the pipes and try to score as high as possible!");
        }
        
        function initGame(difficulty) {
            bird = { x: 50, y: canvas.height / 2, radius: 15, velocity: 0 };
            pipes = [];
            frame = 0;
            gameOver = false;
            
            if (difficulty === "easy") {
                gravity = 0.3;
                lift = -6;
                pipeSpeed = 1.5;
                pipeGap = 160;
            } else {
                gravity = 0.5;
                lift = -8;
                pipeSpeed = 2.5;
                pipeGap = 120;
            }
        }
        
        function drawBackground() {
            ctx.drawImage(backgroundImage, backgroundX, 0, canvas.width, canvas.height);
            ctx.drawImage(backgroundImage, backgroundX + canvas.width, 0, canvas.width, canvas.height);
        }
        
        function drawBird() {
            ctx.fillStyle = "yellow";
            ctx.beginPath();
            ctx.arc(bird.x, bird.y, bird.radius, 0, Math.PI * 2);
            ctx.fill();
        }
        
        function drawPipes() {
            ctx.fillStyle = "green";
            pipes.forEach(pipe => {
                ctx.fillRect(pipe.x, 0, pipe.width, pipe.top);
                ctx.fillRect(pipe.x, pipe.bottom, pipe.width, canvas.height - pipe.bottom);
            });
        }
        
        function updatePipes() {
            if (frame % 100 === 0) {
                let topHeight = Math.random() * (canvas.height / 2);
                pipes.push({ x: canvas.width, top: topHeight, bottom: topHeight + pipeGap, width: 50 });
            }
            pipes.forEach(pipe => pipe.x -= pipeSpeed);
            pipes = pipes.filter(pipe => pipe.x + pipe.width > 0);
        }
        
        function checkCollision() {
            if (bird.y + bird.radius >= canvas.height || bird.y - bird.radius <= 0) {
                gameOver = true;
            }
            pipes.forEach(pipe => {
                if (
                    bird.x + bird.radius > pipe.x &&
                    bird.x - bird.radius < pipe.x + pipe.width &&
                    (bird.y - bird.radius < pipe.top || bird.y + bird.radius > pipe.bottom)
                ) {
                    gameOver = true;
                }
            });
        }
        
        function update() {
            if (gameOver) {
                ctx.fillStyle = "red";
                ctx.font = "30px Arial";
                ctx.fillText("Game Over!", canvas.width / 2 - 90, canvas.height / 2);
                return;
            }
            ctx.clearRect(0, 0, canvas.width, canvas.height);
            
            backgroundX -= backgroundSpeed;
            if (backgroundX <= -canvas.width) {
                backgroundX = 0;
            }
            
            drawBackground();
            drawBird();
            drawPipes();
            bird.velocity += gravity;
            bird.y += bird.velocity;
            updatePipes();
            checkCollision();
            frame++;
            requestAnimationFrame(update);
        }
        
        function startGame() {
            let difficulty = document.getElementById("difficulty").value;
            initGame(difficulty);
            update();
        }
        
        // Allow mobile touch and key press
        document.addEventListener("keydown", () => { bird.velocity = lift; });
        canvas.addEventListener("click", () => { bird.velocity = lift; });  // Mobile tap support
    </script>
</body>
</html>

