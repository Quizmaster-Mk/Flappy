<!DOCTYPE html>
<html lang="fr">
<head>
<meta charset="UTF-8">
<title>Flappy Comic Bird</title>
<style>
  body {
    margin: 0;
    background: #f5f5dc; /* fond clair style comics */
    display: flex;
    justify-content: center;
    align-items: center;
    height: 100vh;
  }
  canvas {
    background: linear-gradient(to top, #87ceeb, #ffffff); /* ciel comic */
    border: 4px solid #000;
  }
</style>
</head>
<body>
<canvas id="gameCanvas" width="400" height="600"></canvas>
<script>
const canvas = document.getElementById("gameCanvas");
const ctx = canvas.getContext("2d");

const bird = { x: 50, y: 300, width: 40, height: 30, dy: 0 };
const gravity = 0.6;
const jump = -10;

let pipes = [];
let frame = 0;
let score = 0;
let gameOver = false;

// Créer un nouveau pipe
function createPipe() {
  const gap = 150;
  const minHeight = 50;
  const maxHeight = canvas.height - gap - 50;
  const height = Math.floor(Math.random() * (maxHeight - minHeight + 1) + minHeight);
  pipes.push({ x: canvas.width, y: 0, width: 60, height });
  pipes.push({ x: canvas.width, y: height + gap, width: 60, height: canvas.height - height - gap });
}

// Détecter collision
function collision(b, p) {
  return b.x < p.x + p.width && b.x + b.width > p.x &&
         b.y < p.y + p.height && b.y + b.height > p.y;
}

// Écouter espace ou clic
document.addEventListener("keydown", e => {
  if(e.code === "Space" || e.key === " ") bird.dy = jump;
});
canvas.addEventListener("click", () => bird.dy = jump);

// Boucle du jeu
function update() {
  if(gameOver) return;

  ctx.clearRect(0, 0, canvas.width, canvas.height);

  // Dessiner le bird (style comic)
  ctx.fillStyle = "red";
  ctx.fillRect(bird.x, bird.y, bird.width, bird.height);

  // Appliquer gravité
  bird.dy += gravity;
  bird.y += bird.dy;

  // Créer pipes toutes les 100 frames
  if(frame % 100 === 0) createPipe();

  // Déplacer et dessiner pipes
  ctx.fillStyle = "limegreen";
  for(let i = 0; i < pipes.length; i++){
    pipes[i].x -= 2;
    ctx.fillRect(pipes[i].x, pipes[i].y, pipes[i].width, pipes[i].height);

    if(collision(bird, pipes[i])) gameOver = true;
    // Incrémenter score quand pipe est passé
    if(pipes[i].x + pipes[i].width === bird.x) score++;
  }

  // Dessiner score style comic
  ctx.fillStyle = "black";
  ctx.font = "30px Comic Sans MS";
  ctx.fillText("Score: " + score, 10, 40);

  // Collision avec le sol ou le plafond
  if(bird.y + bird.height > canvas.height || bird.y < 0) gameOver = true;

  frame++;
  if(!gameOver) requestAnimationFrame(update);
  else {
    ctx.fillStyle = "red";
    ctx.font = "50px Comic Sans MS";
    ctx.fillText("BOOM!", 120, 300);
  }
}

update();
</script>
</body>
</html>
