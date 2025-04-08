<!DOCTYPE html><html lang="fr">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Mr Bilòr - Le Jeu</title>
  <style>
    * { margin: 0; padding: 0; box-sizing: border-box; }
    body { font-family: Arial, sans-serif; background: #222; color: white; overflow: hidden; }
    canvas { background: linear-gradient(to top, #3e3e3e, #1e1e1e); display: block; margin: auto; border: 2px solid #fff; }
    #ui {
      position: absolute;
      top: 10px;
      left: 10px;
      z-index: 10;
      font-size: 18px;
    }
    #menu {
      position: absolute;
      top: 50%;
      left: 50%;
      transform: translate(-50%, -50%);
      text-align: center;
    }
    button {
      padding: 10px 20px;
      font-size: 18px;
      margin-top: 20px;
    }
  </style>
</head>
<body>
  <div id="ui" style="display:none;">
    Score : <span id="score">0</span>
    | Vies : <span id="lives">3</span>
  </div>  <div id="menu">
    <h1>Bienvenue dans Mr Bilòr</h1>
    <p>Évite les blocs, saute, vole et fais un meilleur score !</p>
    <button onclick="startGame()">Jouer</button>
  </div><canvas id="gameCanvas" width="400" height="600"></canvas>

<audio id="jumpSound" src="https://cdn.pixabay.com/download/audio/2021/08/08/audio_5b4d43ba28.mp3?filename=jump-1-194174.mp3"></audio> <audio id="hitSound" src="https://cdn.pixabay.com/download/audio/2021/08/08/audio_f2a8fd3130.mp3?filename=hit-194179.mp3"></audio>

  <script>
    const canvas = document.getElementById("gameCanvas");
    const ctx = canvas.getContext("2d");
    const scoreEl = document.getElementById("score");
    const livesEl = document.getElementById("lives");
    const ui = document.getElementById("ui");
    const menu = document.getElementById("menu");
    const jumpSound = document.getElementById("jumpSound");
    const hitSound = document.getElementById("hitSound");

    let player, blocks, score, lives, gravity, gameInterval;

    function resetGame() {
      player = { x: 50, y: 500, width: 30, height: 30, dy: 0, fly: false };
      blocks = [];
      score = 0;
      lives = 3;
      gravity = 0.5;
    }

    function drawPlayer() {
      ctx.fillStyle = "yellow";
      ctx.fillRect(player.x, player.y, player.width, player.height);
    }

    function drawBlocks() {
      ctx.fillStyle = "red";
      blocks.forEach(block => {
        ctx.fillRect(block.x, block.y, block.width, block.height);
      });
    }

    function updateBlocks() {
      blocks.forEach(block => block.x -= 4);
      if (Math.random() < 0.02) {
        blocks.push({ x: canvas.width, y: 550, width: 30, height: 30 });
      }
      blocks = blocks.filter(b => b.x + b.width > 0);
    }

    function detectCollision() {
      blocks.forEach(block => {
        if (
          player.x < block.x + block.width &&
          player.x + player.width > block.x &&
          player.y < block.y + block.height &&
          player.y + player.height > block.y
        ) {
          hitSound.play();
          lives--;
          blocks.splice(blocks.indexOf(block), 1);
          if (lives <= 0) endGame();
        }
      });
    }

    function update() {
      ctx.clearRect(0, 0, canvas.width, canvas.height);

      player.dy += gravity;
      player.y += player.dy;

      if (player.y > canvas.height - player.height) player.y = canvas.height - player.height;
      if (player.y < 0) player.y = 0;

      updateBlocks();
      detectCollision();

      drawPlayer();
      drawBlocks();

      score++;
      scoreEl.textContent = score;
      livesEl.textContent = lives;
    }

    function startGame() {
      resetGame();
      menu.style.display = "none";
      ui.style.display = "block";
      gameInterval = setInterval(update, 1000 / 60);
    }

    function endGame() {
      clearInterval(gameInterval);
      alert("Game Over ! Score : " + score);
      menu.style.display = "block";
      ui.style.display = "none";
    }

    window.addEventListener("keydown", e => {
      if (e.code === "Space") {
        player.dy = -10;
        jumpSound.play();
      }
    });
  </script></body>
</html>
