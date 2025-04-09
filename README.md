from zipfile import ZipFile

# Recriar o conteúdo do index.html após o reset
html_content = """
<!DOCTYPE html>
<html lang="pt-BR">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0" />
  <title>Surpresa 😘</title>
  <style>
    body {
      background-color: #ff66cc;
      font-family: 'Comic Sans MS', cursive;
      display: flex;
      flex-direction: column;
      align-items: center;
      justify-content: center;
      height: 100vh;
      margin: 0;
      overflow: hidden;
      background-image: url('data:image/svg+xml;utf8,<svg xmlns="http://www.w3.org/2000/svg" width="100" height="100"><text x="0" y="20" font-size="20" fill="%23ff1493">❤</text></svg>');
      background-repeat: repeat;
    }
    .heart-button {
      width: 150px;
      height: 80px;
      background-color: #ffffff;
      border-radius: 20px;
      border: 3px solid #ff1493;
      position: absolute;
      display: flex;
      flex-direction: column;
      align-items: center;
      justify-content: center;
      color: #ff1493;
      font-size: 18px;
      font-weight: bold;
      cursor: pointer;
      text-align: center;
      user-select: none;
      box-shadow: 0 4px 8px rgba(0, 0, 0, 0.2);
    }
    .scratch-container {
      display: none;
      flex-direction: row;
      gap: 20px;
      margin-top: 20px;
    }
    .scratch-card {
      width: 200px;
      height: 120px;
      position: relative;
      background: #000;
      cursor: pointer;
      user-select: none;
    }
    canvas {
      position: absolute;
      top: 0;
      left: 0;
    }
    .scratch-message {
      position: absolute;
      top: 50%;
      left: 50%;
      transform: translate(-50%, -50%);
      font-size: 20px;
      color: #ff1493;
      text-align: center;
      font-family: 'Brush Script MT', cursive;
      white-space: pre-line;
    }
    .heart {
      position: absolute;
      width: 20px;
      height: 20px;
      background-color: #ff69b4;
      clip-path: path('M 10 0 C 13 -3, 20 3, 10 15 C 0 3, 7 -3, 10 0 Z');
      animation: float 2s ease-out forwards;
    }
    @keyframes float {
      0% { transform: translateY(0); opacity: 1; }
      100% { transform: translateY(-200px); opacity: 0; }
    }
    .confetti {
      position: fixed;
      width: 10px;
      height: 10px;
      background-color: #fff;
      border-radius: 50%;
      animation: confetti-fall 2s ease-out forwards;
    }
    @keyframes confetti-fall {
      0% { transform: translateY(0); opacity: 1; }
      100% { transform: translateY(300px); opacity: 0; }
    }
    .final-message {
      display: none;
      font-size: 48px;
      color: #fff;
      font-weight: bold;
      text-align: center;
      z-index: 999;
    }
  </style>
</head>
<body>
  <div id="heartButton" class="heart-button">
    <div>CLIQUE</div>
    <div>AQUI</div>
  </div>

  <div class="scratch-container" id="scratchContainer">
    <div class="scratch-card"><div class="scratch-message">💔 TENTE DE NOVO 😢</div><canvas width="200" height="120"></canvas></div>
    <div class="scratch-card"><div class="scratch-message">💔 TENTE DE NOVO 😢</div><canvas width="200" height="120"></canvas></div>
    <div class="scratch-card" id="finalCard"><div class="scratch-message">❤️ LIBERA O CANECO ❤️</div><canvas width="200" height="120"></canvas></div>
  </div>

  <div class="final-message" id="finalMessage">❤️ LIBERA O CANECO ❤️</div>
  <audio id="music" src="https://dl.dropboxusercontent.com/scl/fi/bt2zq8khvqnmp9do4cbhq/Careless-Whisper.mp3?rlkey=0ayb60el74mqm0bvdc1qpkel2&dl=0"></audio>

  <script>
    const heartButton = document.getElementById('heartButton');
    const scratchContainer = document.getElementById('scratchContainer');
    const music = document.getElementById('music');
    const finalMessage = document.getElementById('finalMessage');
    let clickCount = 0;

    function moveHeart() {
      heartButton.style.top = Math.random() * 80 + '%';
      heartButton.style.left = Math.random() * 80 + '%';
    }

    function createHeart() {
      const heart = document.createElement('div');
      heart.classList.add('heart');
      heart.style.left = Math.random() * 100 + 'vw';
      heart.style.top = '100vh';
      document.body.appendChild(heart);
      setTimeout(() => heart.remove(), 2000);
    }

    function createConfetti() {
      for (let i = 0; i < 300; i++) {
        const confetti = document.createElement('div');
        confetti.classList.add('confetti');
        confetti.style.left = Math.random() * 100 + 'vw';
        confetti.style.top = Math.random() * 10 + 'vh';
        confetti.style.backgroundColor = `hsl(${Math.random() * 360}, 100%, 75%)`;
        document.body.appendChild(confetti);
        setTimeout(() => confetti.remove(), 2000);
      }
    }

    heartButton.onclick = () => {
      clickCount++;
      createHeart();
      if (clickCount >= 8) {
        heartButton.style.display = 'none';
        scratchContainer.style.display = 'flex';
        prepareAllScratchCards();
      } else {
        moveHeart();
      }
    };

    function prepareAllScratchCards() {
      const cards = document.querySelectorAll('.scratch-card');
      cards.forEach(card => {
        const canvas = card.querySelector('canvas');
        const ctx = canvas.getContext('2d');
        ctx.globalCompositeOperation = 'source-over';
        ctx.fillStyle = '#000';
        ctx.fillRect(0, 0, canvas.width, canvas.height);

        let isDrawing = false;

        function scratch(e) {
          if (!isDrawing) return;
          const rect = canvas.getBoundingClientRect();
          const x = (e.clientX || e.touches?.[0].clientX) - rect.left;
          const y = (e.clientY || e.touches?.[0].clientY) - rect.top;
          ctx.globalCompositeOperation = 'destination-out';
          ctx.beginPath();
          ctx.arc(x, y, 20, 0, Math.PI * 2);
          ctx.fill();
        }

        canvas.addEventListener('mousedown', () => isDrawing = true);
        canvas.addEventListener('mouseup', () => isDrawing = false);
        canvas.addEventListener('mousemove', scratch);
        canvas.addEventListener('touchstart', () => isDrawing = true);
        canvas.addEventListener('touchend', () => isDrawing = false);
        canvas.addEventListener('touchmove', (e) => {
          const touch = e.touches[0];
          const mouseEvent = new MouseEvent('mousemove', {
            clientX: touch.clientX,
            clientY: touch.clientY
          });
          canvas.dispatchEvent(mouseEvent);
        });
      });

      const finalCard = document.getElementById('finalCard');
      const finalCanvas = finalCard.querySelector('canvas');
      finalCanvas.addEventListener('mouseup', () => setTimeout(() => triggerFinalEffect(), 300));
      finalCanvas.addEventListener('touchend', () => setTimeout(() => triggerFinalEffect(), 300));
    }

    function triggerFinalEffect() {
      createConfetti();
      music.play();
      document.getElementById("scratchContainer").style.display = "none";
      finalMessage.style.display = "block";
    }

    setInterval(createHeart, 500);
    moveHeart();
  </script>
</body>
</html>
"""

# Salvar arquivo HTML
file_path = "/mnt/data/index.html"
with open(file_path, "w", encoding="utf-8") as f:
    f.write(html_content)

# Criar ZIP
zip_path = "/mnt/data/surpresa_namorado.zip"
with ZipFile(zip_path, "w") as zipf:
    zipf.write(file_path, arcname="index.html")

zip_path
