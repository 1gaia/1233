<!DOCTYPE html>
<html lang="ko">
<head>
  <meta charset="UTF-8" />
  <title>사과 합치기 게임 - 로그인 & 난이도 선택</title>
  <style>
    body {
      font-family: Arial, sans-serif;
      text-align: center;
      margin: 30px;
    }
    .container {
      max-width: 400px;
      margin: 0 auto;
      border: 2px solid #ddd;
      padding: 20px;
      border-radius: 10px;
    }
    input[type="text"], input[type="password"], select {
      width: 90%;
      padding: 10px;
      margin: 10px 0;
      font-size: 16px;
      border-radius: 5px;
      border: 1px solid #ccc;
      box-sizing: border-box;
    }
    button {
      padding: 10px 20px;
      font-size: 16px;
      border-radius: 5px;
      cursor: pointer;
      background-color: #5cb85c;
      color: white;
      border: none;
      margin-top: 10px;
      margin-right: 5px;
    }
    button:hover {
      background-color: #4cae4c;
    }
    .hidden {
      display: none;
    }
    .tabs {
      display: flex;
      justify-content: center;
      margin-bottom: 20px;
      gap: 10px;
    }
    .tab {
      flex: 1;
      padding: 10px;
      cursor: pointer;
      border-bottom: 2px solid transparent;
      user-select: none;
    }
    .tab.active {
      border-bottom: 2px solid #5cb85c;
      font-weight: bold;
    }
    #apple-list {
      display: flex;
      flex-wrap: wrap;
      justify-content: center;
      gap: 10px;
      margin-top: 20px;
    }
    .apple {
      width: 70px;
      height: 70px;
      font-size: 38px;
      position: relative;
      cursor: pointer;
      display: flex;
      justify-content: center;
      align-items: center;
      transition: transform 0.2s;
    }
    .apple:hover {
      transform: scale(1.1);
    }
    .apple::before {
      content: "🍎";
      position: absolute;
      font-size: 42px;
      top: 0;
      left: 0;
      right: 0;
      bottom: 0;
      display: flex;
      justify-content: center;
      align-items: center;
    }
    .apple span {
      z-index: 1;
      font-weight: bold;
      color: white;
      font-size: 30px;
      text-shadow: 1px 1px 2px black;
    }
    .apple.selected {
      background-color: gold;
      border-radius: 50%;
      border: 4px solid red;
      animation: blink 0.6s infinite alternate;
    }
    @keyframes blink {
      from { transform: scale(1); opacity: 1; }
      to { transform: scale(1.05); opacity: 0.7; }
    }
    #score-display {
      margin-top: 10px;
      font-size: 18px;
      font-weight: bold;
    }
    #logout-btn {
      position: absolute;
      top: 10px;
      right: 10px;
    }
  </style>
</head>
<body>

<!-- 로그아웃 버튼 -->
<button id="logout-btn" class="hidden">로그아웃</button>

<!-- 로그인 & 회원가입 -->
<div class="container" id="auth-container">
  <div class="tabs">
    <div class="tab active" id="login-tab">로그인</div>
    <div class="tab" id="signup-tab">회원가입</div>
  </div>

  <div id="login-form">
    <input type="text" id="login-username" placeholder="아이디" />
    <input type="password" id="login-password" placeholder="비밀번호" />
    <button id="login-btn">로그인</button>
  </div>

  <div id="signup-form" class="hidden">
    <input type="text" id="signup-username" placeholder="아이디" />
    <input type="password" id="signup-password" placeholder="비밀번호" />
    <button id="signup-btn">회원가입</button>
  </div>
</div>

<!-- 난이도 선택 -->
<div class="container hidden" id="difficulty-container">
  <h2>난이도 선택</h2>
  <select id="difficulty-select">
    <option value="easy">쉬움</option>
    <option value="normal" selected>보통</option>
    <option value="hard">어려움</option>
  </select>
  <br />
  <button id="start-game-btn">게임 시작</button>
</div>

<!-- 게임 화면 -->
<div class="container hidden" id="game-container">
  <h2>사과 합치기 게임</h2>
  <div id="score-display">점수: 0점 / 남은 사과: 0개</div>
  <div id="apple-list"></div>
  <button id="restart-btn" class="hidden">게임 다시 시작</button>
  <button id="back-btn">돌아가기</button>
</div>

<script>
  const savedUsers = localStorage.getItem('users');
  let users = savedUsers ? JSON.parse(savedUsers) : {};

  const loginTab = document.getElementById('login-tab');
  const signupTab = document.getElementById('signup-tab');
  const loginForm = document.getElementById('login-form');
  const signupForm = document.getElementById('signup-form');

  loginTab.addEventListener('click', () => {
    loginTab.classList.add('active');
    signupTab.classList.remove('active');
    loginForm.classList.remove('hidden');
    signupForm.classList.add('hidden');
  });

  signupTab.addEventListener('click', () => {
    signupTab.classList.add('active');
    loginTab.classList.remove('active');
    signupForm.classList.remove('hidden');
    loginForm.classList.add('hidden');
  });

  document.getElementById('signup-btn').addEventListener('click', () => {
    const username = document.getElementById('signup-username').value.trim();
    const password = document.getElementById('signup-password').value.trim();
    if (!username || !password) return alert('아이디와 비밀번호를 입력하세요.');
    if (users[username]) return alert('이미 존재하는 아이디입니다.');
    users[username] = password;
    localStorage.setItem('users', JSON.stringify(users));
    alert('회원가입 성공!');
    signupTab.click();
  });

  document.getElementById('login-btn').addEventListener('click', () => {
    const username = document.getElementById('login-username').value.trim();
    const password = document.getElementById('login-password').value.trim();
    if (!username || !password) return alert('아이디와 비밀번호를 입력하세요.');
    if (users[username] !== password) return alert('아이디 또는 비밀번호가 틀렸습니다.');
    alert(`${username}님, 로그인 성공!`);
    document.getElementById('auth-container').classList.add('hidden');
    document.getElementById('difficulty-container').classList.remove('hidden');
    document.getElementById('logout-btn').classList.remove('hidden');
  });

  document.getElementById('logout-btn').addEventListener('click', () => {
    document.getElementById('auth-container').classList.remove('hidden');
    document.getElementById('difficulty-container').classList.add('hidden');
    document.getElementById('game-container').classList.add('hidden');
    document.getElementById('logout-btn').classList.add('hidden');
    document.getElementById('login-username').value = '';
    document.getElementById('login-password').value = '';
  });

  // 게임 변수
  let applesArray = [];
  let selectedIndices = [];
  let score = 0;
  let currentDifficulty = 'easy';
  const difficultyLevels = ['easy', 'normal', 'hard'];

  function getAppleCountByDifficulty(difficulty) {
    const basePairs = 10;
    if (difficulty === 'easy') return basePairs;
    if (difficulty === 'normal') return basePairs + 3;
    if (difficulty === 'hard') return basePairs + 6;
    return basePairs;
  }

  function getNextDifficulty(current) {
    const idx = difficultyLevels.indexOf(current);
    return difficultyLevels[idx + 1] || null;
  }

  function updateScore() {
    document.getElementById('score-display').textContent =
      `점수: ${score}점 / 남은 사과: ${applesArray.length}개`;
  }

  function renderApples() {
    const appleList = document.getElementById('apple-list');
    appleList.innerHTML = '';
    applesArray.forEach((num, idx) => {
      const apple = document.createElement('div');
      apple.className = 'apple';
      if (selectedIndices.includes(idx)) apple.classList.add('selected');
      apple.innerHTML = `<span>${num}</span>`;
      apple.addEventListener('click', () => handleAppleClick(idx));
      appleList.appendChild(apple);
    });
  }

  function handleAppleClick(index) {
    const i = selectedIndices.indexOf(index);
    if (i !== -1) {
      selectedIndices.splice(i, 1);
    } else {
      selectedIndices.push(index);
    }

    renderApples();

    const sum = selectedIndices.reduce((acc, i) => acc + applesArray[i], 0);
    if (sum === 10) {
      selectedIndices.sort((a, b) => b - a).forEach(i => applesArray.splice(i, 1));
      score += 10;
      selectedIndices = [];
      renderApples();
      updateScore();

      if (applesArray.length === 0) {
        const next = getNextDifficulty(currentDifficulty);
        if (next) {
          alert(`🎉 ${currentDifficulty} 난이도 클리어! 다음 단계로 넘어갑니다.`);
          startGame(next);
        } else {
          alert("🎊 모든 난이도를 클리어했습니다! 게임 종료.");
          document.getElementById('restart-btn').classList.remove('hidden');
        }
      }
    } else if (sum > 10) {
      alert("❌ 합이 10을 초과했어요!");
      selectedIndices = [];
      renderApples();
    }
  }

  function generateApples(pairCount) {
    const arr = [];
    for (let i = 0; i < pairCount; i++) {
      const n = Math.floor(Math.random() * 9) + 1;
      const m = 10 - n;
      arr.push(n, m);
    }
    return arr.sort(() => Math.random() - 0.5);
  }

  function startGame(difficulty = null) {
    currentDifficulty = difficulty || document.getElementById('difficulty-select').value;
    const pairCount = getAppleCountByDifficulty(currentDifficulty);
    applesArray = generateApples(pairCount);
    score = 0;
    selectedIndices = [];
    document.getElementById('difficulty-container').classList.add('hidden');
    document.getElementById('game-container').classList.remove('hidden');
    document.getElementById('restart-btn').classList.add('hidden');
    renderApples();
    updateScore();
  }

  document.getElementById('start-game-btn').addEventListener('click', () => startGame());
  document.getElementById('restart-btn').addEventListener('click', () => startGame());

  document.getElementById('back-btn').addEventListener('click', () => {
    document.getElementById('game-container').classList.add('hidden');
    document.getElementById('difficulty-container').classList.remove('hidden');
    applesArray = [];
    selectedIndices = [];
    score = 0;
  });
</script>

</body>
</html>
