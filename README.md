# Typing-Speed-Racer
Game
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <link rel="manifest" href="manifest.json">
  <title>Typing Speed Racer</title>
  <style>
     body {
            margin: 0;
            overflow: hidden;
            padding: 0;
            font-family: Arial, sans-serif;
            height: 100vh;
            display: flex;
            justify-content: center;
            align-items: center;
            background: linear-gradient(120deg, #008467, #0f4e72, #00c6ff);
            background-size: 300% 300%;
            animation: gradientAnimation 8s ease infinite;
            color: white;
        }
         @keyframes gradientAnimation {
            0% { background-position: 0% 50%; }
            50% { background-position: 100% 50%; }
            100% { background-position: 0% 50%; }
        }

    .game-container {
      width: 80%;
      margin: 20px auto;
      position: relative;
    }

    .track {
      position: relative;
      height: 300px;
      margin: 20px 0;
      background: #333;
      border: 2px solid #fff;
      border-radius: 10px;
      box-shadow: 0px 10px 30px rgba(0, 0, 0, 0.5);
      overflow: hidden;
    }

    .road-lines {
      position: absolute;
      top: 50%;
      left: 0;
      width: 100%;
      height: 5px;
      background-color: #fff;
      transform: translateY(-50%);
    }

    .dashed-lines {
      position: absolute;
      top: 50%;
      left: 0;
      width: 100%;
      height: 5px;
      background-color: #fff;
      transform: translateY(-50%);
      background-image: repeating-linear-gradient(to right, transparent, transparent 20px, #fff 20px, #fff 40px);
    }

    .car {
      width: 50px;
      height: 30px;
      position: absolute;
      bottom: 10px;
      transition: left 0.5s ease;
      background-size: contain;
      background-repeat: no-repeat;
      background-position: center;
    }

    .player-car {
      background-image: url('player-car.png');
    }

    .computer-car {
      background-image: url('computer-car.png');
      top: 50px;
    }

    .word-display {
      margin: 20px 0;
      font-size: 50px;
    }

    .input-box {
      padding: 15px;
      font-size: 20px;
      width: 90%;
    }

    .progress-container {
      margin-top: 30px;
      display: flex;
      justify-content: space-around;
      color: #cyan;
    }

    .player-progress,
    .computer-progress {
      width: 40%;
      height: 10px;
      background: red ;
      transition: width 0.5s ease;
    }

    .level {
      font-size: 30px;
      margin-top: 10px;
    }

    .notification {
      position: center;
      width: 90%;
      height: 50%;
      top: 20px;
      right: 20px;
      background: rgba(1, 8, 0, 0.8);
      color: #RED;
      padding: 15px 20px;
      border-radius: 8px;
      box-shadow: 0 5px 15px rgba(0, 0, 0, 0.5);
      font-size: 48px;
      z-index: 1000;
      display: none;
      opacity: 0;
      animation: fade-in-out 20s forwards;
    }

    .notification button {
      margin-top: 10px;
      padding: 10px;
      background-color: #fff;
      color: #000;
      border: none;
      border-radius: 2px;
      cursor: pointer;
    }

    @keyframes fade-in-out {
      0% {
        opacity: 0;
        transform: translateY(-10px);
      }
      10% {
        opacity: 1;
        transform: translateY(0);
      }
      90% {
        opacity: 1;
        transform: translateY(0);
      }
      100% {
        opacity: 0;
        transform: translateY(-10px);
      }
    }

    .button-container {
      margin-top: 20px;
    }

    .button-container button {
      padding: 15px 25px;
      margin: 0 10px;
      background-color: #4CAF50;
      color: white;
      font-size: 18px;
      border: none;
      border-radius: 5px;
      cursor: pointer;
    }

    .button-container button:hover {
      background-color: #000000;
    }

    .typing-speed {
      font-size: 20px;
      margin-top: 20px;
      color: yellow;
    }
  </style>
</head>
<body background="bg2.jpg">
  <div class="game-container">
    <div class="track">
      <div class="road-lines"></div>
      <div class="dashed-lines"></div>
      <div class="car player-car"></div>
      <div class="car computer-car"></div>
    </div>
    <div class="word-display">
      <span class="word">example</span>
    </div>
    <input type="text" class="input-box" placeholder="Type here..." />
    <div class="progress-container">
      <div class="player-progress"></div>
      <div class="computer-progress"></div>
    </div>
    <div class="level">Level: <span id="level-number">1</span></div>
    <div class="button-container">
      <button id="restart-button">Restart</button>
    </div>
    <div class="typing-speed" id="typing-speed">Speed: 0 WPM</div>
  </div>
<center>
  <div  class="notification" id="notification">

    Your Progress <br>

    <font color="YELLOW">
    <br>    <span id="notification-message"></span>
    <br>
</font>
    <button id="notification-button"></button>
  </div>
  
  </center>
<script>
  const wordDisplay = document.querySelector(".word");
  const inputBox = document.querySelector(".input-box");
  const playerCar = document.querySelector(".player-car");
  const computerCar = document.querySelector(".computer-car");
  const playerProgress = document.querySelector(".player-progress");
  const computerProgress = document.querySelector(".computer-progress");
  const levelDisplay = document.querySelector("#level-number");
  const notification = document.querySelector("#notification");
  const notificationMessage = document.querySelector("#notification-message");
  const notificationButton = document.querySelector("#notification-button");
  const restartButton = document.querySelector("#restart-button");
  const typingSpeedDisplay = document.querySelector("#typing-speed");

  let currentWord = "";
  let playerPosition = 0;
  let computerPosition = 0;
  let level = 1;
  let playerSpeed = 20;
  let computerSpeed = 1;
  let startTime = 0;
  let wordsTyped = 0;

 const wordLevels = [
    ["rat", "hat", "mat", "jet", "set",
    "bet", "pin", "fan", "pan", "ran",
    "bun", "fun", "win", "men", "hen",
    "tin", "cup", "pup", "tap", "map",
    "nap", "lap", "lip", "rip", "dip",
    "pot", "cot", "lot", "bot", "top",
    "mop", "nap", "fox", "box", "socks",
    "wig", "fig", "pig", "leg", "beg",
    "tag", "bag", "rag", "jug", "bug",
    "rug", "tug", "cup", "sup", "yup",
    "zip", "tip", "lip", "rip", "dip",
    "nip", "sip", "pip", "lip", "fip",
    "bun", "sun", "run", "pun",
    "bud", "mud", "rug", "jug", "hug",
    "pig", "dig", "fig", "wig", "rig",
    "map", "cap", "tap", "zap", "nap",
    "pot", "dot", "hot", "lot", "cot",
    "bat", "rat", "mat", "pat", "fat"],

    ["stone", "water", "music", "plant", "lunar",
    "peace", "heart", "world", "house", "paint",
    "feast", "bride", "sword", "cloud", "sweet",
    "light", "smile", "grind", "alert", "dream",
    "flame", "sweet", "brush", "plane", "bacon",
    "sharp", "stone", "beach", "dance", "bring",
    "fight", "trust", "shine", "latch", "piano",
    "color", "table", "frank", "leafy", "speed",
    "jumpy", "ghost", "scout", "drink", "clean",
    "chose", "straw", "sword", "plaza", "grape",
    "vivid", "glove", "clash", "frill", "brick",
    "stare", "smash", "pearl", "blaze",
    "creek", "swirl", "trick", "block"],

    ["mountain", "elephant", "parachute", "umbrella", "dolphin", "absolute", "accurate", "advanced", "backpack", "baseball",
    "bathroom", "campaign", "celebrate", "chilling", "cleaning",
    "coloring", "computer", "customer", "daughter", "defender",
    "designer", "distance", "elevator", "employee", "engineer",
    "everyday", "explorer", "exposure", "festival", "football",
    "forehead", "frontier", "gardener", "grateful", "homeland",
    "hospital", "ignition", "industry", "internet", "keyboard",
    "language", "lifeline", "magazine", "majestic", "maritime",
    "momentum", "monopoly", "mountain", "mushroom", "national",
    "operator", "overcome", "paradise", "physical", "platform",
    "pleasure", "postcard", "railroad", "resource", "security",
    "spectrum", "standard", "strategy", "strength", "students",
    "sunlight", "surprise", "tailored", "template", "tracking",
    "treasure", "vacation", "velocity", "vertical", "villages",
    "vineyard", "workshop", "workflow", "wireless", "wildlife"
],

    ["absentee", "adventure", "alligator", "appearance", "balancer", "beginning", "campaigns", "carefully", "catalogue", "conductor",
"cultural", "diminished", "detection", "different", "dormitory", "editorial", "elevation", "employee", "expression", "financial",
"fireplace", "fossilized", "genuine", "hilarity", "illusion", "incident", "informed", "innovation", "integrate", "jeopardize",
"landscape", "librarian", "magnitude", "monitoring", "newsletter", "navigator", "operation", "optimistic", "overload", "particular",
"platforms", "prevention", "president", "principle", "provision", "realistic", "relevant", "resident", "secluded", "solution",
"strategy", "tolerable", "universe", "vibrantly", "vocations", "wonderful", "workplace", "workshop", "yellowish", "admissions",
"baseplate", "breathed", "commandos", "contribute", "curiosity", "digitized", "empowered", "explained", "expertise", "feedback",
"formulate", "generator", "historian", "innovation", "laughter", "legitimate", "motivation", "negotiated", "objective", "overcome",
"participate", "philanthropy", "preserved", "realistic", "residence", "resemble", "respectful", "simplified", "streaming",
"survival", "tradition", "unspoken", "vulnerable", "wholesome", "workforce", "transmit", "permanent"]
];


  function startGame() {
    currentWord = getNewWord();
    wordDisplay.textContent = currentWord;
    inputBox.value = "";
    levelDisplay.textContent = level;
    computerRace();
    startTime = Date.now();
    wordsTyped = 0;
    typingSpeedDisplay.textContent = `Speed: 0 WPM`;
  }

  function getNewWord() {
    const difficultyIndex = Math.min(Math.floor((level - 1) / 5), wordLevels.length - 1);
    const words = wordLevels[difficultyIndex];
    return words[Math.floor(Math.random() * words.length)];
  }

  inputBox.addEventListener("input", () => {
    if (inputBox.value.trim() === currentWord) {
      playerPosition += playerSpeed;
      playerCar.style.left = playerPosition + "px";
      playerProgress.style.width = (playerPosition / 500) * 100 + "%";
      currentWord = getNewWord();
      wordDisplay.textContent = currentWord;
      inputBox.value = "";

      wordsTyped++;

      const elapsedTime = (Date.now() - startTime) / 1000;
      const wpm = Math.round((wordsTyped / elapsedTime) * 60);
      typingSpeedDisplay.textContent = `Speed: ${wpm} WPM`;

      if (playerPosition >= 500) {
        endLevel(true, wpm);
      }
    }
  });

  function computerRace() {
    const interval = setInterval(() => {
      computerPosition += Math.random() * computerSpeed + 0.5;
      computerCar.style.left = computerPosition + "px";
      computerProgress.style.width = (computerPosition / 500) * 100 + "%";

      if (playerPosition >= 500 || computerPosition >= 500) {
        clearInterval(interval);
        if (computerPosition >= 500) endLevel(false, 0);
      }
    }, 100);
  }

  function calculateStars(wpm) {
    if (wpm >= 50) return "★★★★★"; // 5 stars
    else if (wpm >= 40) return "★★★★☆"; // 4 stars
    else if (wpm >= 30) return "★★★☆☆"; // 3 stars
    else if (wpm >= 20) return "★★☆☆☆"; // 2 stars
    else return "★☆☆☆☆"; // 1 star
  }

  function showNotification(message, wpm, buttonText, buttonAction) {
    const stars = calculateStars(wpm);
    notificationMessage.innerHTML = `
      <p>${message}</p>
      <p>Performance: ${stars}</p>
    `;
    notificationButton.textContent = buttonText;
    notificationButton.onclick = buttonAction;
    notification.style.display = "block";
    setTimeout(() => {
      notification.style.display = "none";
    }, 5000);
  }

  function endLevel(playerWon, wpm) {
  if (playerWon) {
    level++;  // Increase the level when the player wins
    playerPosition = 0;
    computerPosition = 0;
    computerSpeed += 0.5;
    showNotification(`You Win! Level ${level - 1} Score: ${wpm} WPM`, wpm, "Next Level", startGame);
  } else {
    showNotification(`You Lose! Final Score: ${wpm} WPM`, wpm, "Restart", resetGame);
  }
}

  function resetGame() {
  level = 1;  // Reset the level back to 1 when restarting
  playerPosition = 0;
  computerPosition = 0;
  playerSpeed = 20;
  computerSpeed = 1;
  levelDisplay.textContent = level;
  showNotification("Game Reset! Starting Over.", 0, "Start Over", startGame);
}

 if ("serviceWorker" in navigator) {
    navigator.serviceWorker.register("service-worker.js")
      .then((registration) => {
        console.log("Service Worker registered with scope:", registration.scope);
      })
      .catch((error) => {
        console.error("Service Worker registration failed:", error);
      });
  }
  restartButton.addEventListener("click", resetGame);

  startGame();


</script>

</body>
</html>

