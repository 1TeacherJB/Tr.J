<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0" />
  <title>Nanuri Explorer</title>
  <style>
    body {
      margin: 0;
      font-family: 'Segoe UI', sans-serif;
      background: linear-gradient(to right, #8EC5FC, #E0C3FC);
      display: flex;
      justify-content: center;
      align-items: center;
      height: 100vh;
    }
    .welcome-screen, .game-screen, .complete-screen {
      display: none;
      flex-direction: column;
      align-items: center;
      background: rgba(255, 255, 255, 0.8);
      padding: 2rem;
      border-radius: 20px;
      backdrop-filter: blur(10px);
      box-shadow: 0 0 10px rgba(0,0,0,0.1);
    }
    .active { display: flex; }
    button { padding: 10px 20px; margin-top: 1rem; cursor: pointer; border: none; border-radius: 8px; background-color: #4CAF50; color: white; font-size: 1rem; }
    input[type="text"] { padding: 10px; border-radius: 8px; border: 1px solid #ccc; margin-top: 1rem; font-size: 1rem; }
    .progress-bar {
      height: 20px;
      background: #ddd;
      border-radius: 10px;
      margin: 20px 0;
      width: 100%;
      max-width: 300px;
      overflow: hidden;
    }
    .progress {
      height: 100%;
      background: #4CAF50;
      width: 0%;
      transition: width 0.3s;
    }
    .leaderboard { margin-top: 2rem; text-align: left; }
  </style>
</head>
<body>
  <div class="welcome-screen active" id="welcome">
    <h1>Welcome to Nanuri Explorer</h1>
    <p>Enter your name to begin the challenge</p>
    <input type="text" id="playerName" placeholder="Your Name" />
    <button onclick="startGame()">Start Exploring</button>
  </div>

  <div class="game-screen" id="game">
    <h2>Explorer: <span id="currentPlayer"></span></h2>
    <p>Scan QR Codes at each school location</p>
    <button onclick="scanLocation()">Scan QR Code</button>
    <div class="progress-bar"><div class="progress" id="progressBar"></div></div>
    <p>Checked Locations: <span id="checkedCount">0</span>/9</p>
  </div>

  <div class="complete-screen" id="complete">
    <h2>🎉 Well done, <span id="winnerName"></span>! 🎉</h2>
    <p>You completed the challenge!</p>
    <button onclick="restartGame()">Play Again</button>
    <div class="leaderboard">
      <h3>🏆 Leaderboard</h3>
      <ol id="leaderboardList"></ol>
    </div>
  </div>

  <script>
    const locationsTotal = 9;
    let player = '';
    let checked = 0;
    let leaderboard = JSON.parse(localStorage.getItem('nanuriLeaderboard')) || [];

    function startGame() {
      const nameInput = document.getElementById('playerName').value.trim();
      if (!nameInput) return alert('Please enter your name');
      player = nameInput;
      document.getElementById('currentPlayer').innerText = player;
      document.getElementById('welcome').classList.remove('active');
      document.getElementById('game').classList.add('active');
    }

    function scanLocation() {
      if (checked >= locationsTotal) return;
      checked++;
      updateProgress();
      if (checked === locationsTotal) completeChallenge();
    }

    function updateProgress() {
      const percent = (checked / locationsTotal) * 100;
      document.getElementById('checkedCount').innerText = checked;
      document.getElementById('progressBar').style.width = `${percent}%`;
    }

    function completeChallenge() {
      document.getElementById('game').classList.remove('active');
      document.getElementById('complete').classList.add('active');
      document.getElementById('winnerName').innerText = player;
      leaderboard.push({ name: player, time: new Date().getTime() });
      leaderboard.sort((a, b) => a.time - b.time);
      leaderboard = leaderboard.slice(0, 10);
      localStorage.setItem('nanuriLeaderboard', JSON.stringify(leaderboard));
      renderLeaderboard();
    }

    function renderLeaderboard() {
      const list = document.getElementById('leaderboardList');
      list.innerHTML = '';
      leaderboard.forEach(entry => {
        const item = document.createElement('li');
        item.textContent = entry.name;
        list.appendChild(item);
      });
    }

    function restartGame() {
      checked = 0;
      updateProgress();
      document.getElementById('checkedCount').innerText = '0';
      document.getElementById('complete').classList.remove('active');
      document.getElementById('welcome').classList.add('active');
    }

    document.addEventListener('keydown', (e) => {
      const num = parseInt(e.key);
      if (!isNaN(num) && num >= 1 && num <= 9) {
        scanLocation();
      }
    });
  </script>
</body>
</html>
