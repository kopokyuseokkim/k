[gugudan.html](https://github.com/user-attachments/files/26337106/gugudan.html)
<!DOCTYPE html>
<html lang="ko">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>아빠의 선물 - 구구단 게임</title>
    <style>
        :root { --bg: #FFF9F0; --main: #FFD93D; --sub: #6BCB77; --point: #FF6B6B; }
        body { font-family: 'Nanum Gothic', sans-serif; background-color: var(--bg); text-align: center; margin: 0; padding: 15px; user-select: none; -webkit-tap-highlight-color: transparent; }
        #game-box { max-width: 480px; margin: 20px auto; background: white; border-radius: 35px; padding: 25px; box-shadow: 0 10px 30px rgba(0,0,0,0.08); min-height: 80vh; display: flex; flex-direction: column; justify-content: center; }

        h1 { color: var(--point); font-size: 1.8rem; margin-bottom: 5px; }
        .desc { color: #999; margin-bottom: 25px; font-size: 1rem; }

        .grid { display: grid; grid-template-columns: repeat(2, 1fr); gap: 15px; }
        button { border: none; border-radius: 20px; font-weight: bold; cursor: pointer; transition: 0.1s; box-shadow: 0 6px 0 rgba(0,0,0,0.1); outline: none; }
        button:active { transform: translateY(4px); box-shadow: none; }

        .dan-btn { background: var(--main); height: 75px; font-size: 1.5rem; color: #5d4037; }

        #quiz-screen, #result-screen { display: none; }
        .info { font-size: 1.1rem; color: #bbb; margin-bottom: 15px; }
        .question { font-size: 4.5rem; margin: 25px 0; color: #333; font-weight: 900; letter-spacing: -2px; }
        .opt-btn { background: var(--sub); color: white; height: 90px; font-size: 2.2rem; }

        .score-text { font-size: 4rem; color: var(--point); font-weight: 900; margin: 20px 0; }
        .home-btn { background: var(--point); color: white; padding: 20px; width: 100%; font-size: 1.5rem; margin-top: 30px; }
    </style>
</head>
<body>

<div id="game-box">
    <div id="main-screen">
        <h1>🌟 구구단 박사님</h1>
        <p class="desc">몇 단을 도전해볼까요?</p>
        <div class="grid" id="dan-list"></div>
    </div>

    <div id="quiz-screen">
        <div class="info" id="current-info">문제 1 / 10</div>
        <div class="question" id="q-text">2 × 1</div>
        <div class="grid" id="opt-list"></div>
    </div>

    <div id="result-screen">
        <h1>🎉 우와! 다 풀었어요!</h1>
        <p class="desc">나의 점수는?</p>
        <div class="score-text" id="final-score">100점</div>
        <button class="home-btn" onclick="goHome()">처음으로 돌아가기</button>
    </div>
</div>

<audio id="s-click"><source src="https://actions.google.com/sounds/v1/foley/button_click.ogg" type="audio/ogg"></audio>
<audio id="s-correct"><source src="https://actions.google.com/sounds/v1/cartoon/toy_honk.ogg" type="audio/ogg"></audio>
<audio id="s-wrong"><source src="https://actions.google.com/sounds/v1/cartoon/boing.ogg" type="audio/ogg"></audio>
<audio id="s-congrats"><source src="https://actions.google.com/sounds/v1/foley/wind_chime.ogg" type="audio/ogg"></audio>

<script>
    let currentDan = 0, currentIndex = 0, correctAnswers = 0;
    let questions = [];

    const danList = document.getElementById('dan-list');
    for (let i = 2; i <= 9; i++) {
        const btn = document.createElement('button');
        btn.className = 'dan-btn';
        btn.innerText = `${i}단`;
        btn.onclick = () => startGame(i);
        danList.appendChild(btn);
    }

    function changeView(id) {
        playSound('click');
        document.getElementById('main-screen').style.display = 'none';
        document.getElementById('quiz-screen').style.display = 'none';
        document.getElementById('result-screen').style.display = 'none';

        const target = document.getElementById(id);
        target.style.display = 'flex';
        target.style.flexDirection = 'column';
    }

    function startGame(n) {
        currentDan = n;
        currentIndex = 0;
        correctAnswers = 0;
        questions = Array.from({length: 10}, (_, i) => i + 1).sort(() => Math.random() - 0.5);
        changeView('quiz-screen');
        renderQuestion();
    }

    function renderQuestion() {
        const num = questions[currentIndex];
        const answer = currentDan * num;
        document.getElementById('current-info').innerText = `문제 ${currentIndex + 1} / 10`;
        document.getElementById('q-text').innerText = `${currentDan} × ${num}`;

        let options = [answer];
        while(options.length < 4) {
            let fake = currentDan * (Math.floor(Math.random() * 10) + 1);
            if(!options.includes(fake)) options.push(fake);
        }
        options.sort(() => Math.random() - 0.5);

        const list = document.getElementById('opt-list');
        list.innerHTML = '';
        options.forEach(val => {
            const btn = document.createElement('button');
            btn.className = 'opt-btn';
            btn.innerText = val;
            btn.onclick = () => checkAnswer(val, answer);
            list.appendChild(btn);
        });
    }

    function checkAnswer(selected, correct) {
        if (selected === correct) {
            playSound('correct');
            correctAnswers++;
        } else {
            playSound('wrong');
        }

        currentIndex++;
        if (currentIndex < 10) {
            setTimeout(renderQuestion, 400); 
        } else {
            showResult();
        }
    }

    function showResult() {
        playSound('congrats');
        const finalScore = correctAnswers * 10;
        document.getElementById('final-score').innerText = `${finalScore}점`;
        changeView('result-screen');
    }

    function goHome() {
        changeView('main-screen');
    }

    function playSound(id) {
        const audio = document.getElementById('s-' + id);
        if (audio) {
            audio.currentTime = 0;
            audio.play().catch(() => {});
        }
    }
</script>
</body>
</html>
