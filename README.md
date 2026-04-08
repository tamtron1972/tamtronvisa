<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Tamtron Trivia Quiz</title>
<style>
  *, *::before, *::after { box-sizing: border-box; margin: 0; padding: 0; }
  body {
    font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', sans-serif;
    background: #F7F5F2;
    min-height: 100vh;
    display: flex;
    align-items: flex-start;
    justify-content: center;
    padding: 2rem 1rem;
    color: #1a1a1a;
  }
  #quiz-wrap { width: 100%; max-width: 580px; }
  .brand {
    display: flex; align-items: center; gap: 10px; margin-bottom: 2rem;
  }
  .brand-logo {
    width: 32px; height: 32px; background: #E25A30;
    border-radius: 8px; display: flex; align-items: center; justify-content: center;
  }
  .brand-logo svg { width: 18px; height: 18px; fill: white; }
  .brand-name { font-size: 15px; font-weight: 600; color: #E25A30; letter-spacing: 0.06em; }
  .q-header { display: flex; align-items: center; justify-content: space-between; margin-bottom: 10px; }
  .q-count { font-size: 13px; color: #777; }
  .score-count { font-size: 13px; font-weight: 600; color: #E25A30; }
  .progress-bar { height: 6px; background: #E5E2DC; border-radius: 3px; margin-bottom: 1.5rem; overflow: hidden; }
  .progress-fill { height: 100%; background: #E25A30; border-radius: 3px; transition: width 0.4s ease; }
  .question-card {
    background: #fff; border: 1px solid #E8E4DF;
    border-radius: 14px; padding: 1.5rem; margin-bottom: 1rem;
  }
  .question-text { font-size: 18px; font-weight: 600; color: #1a1a1a; line-height: 1.5; }
  .options { display: flex; flex-direction: column; gap: 9px; margin-top: 1.25rem; }
  .opt-btn {
    background: #fff; border: 1px solid #D8D4CF;
    border-radius: 10px; padding: 13px 16px;
    font-size: 15px; color: #1a1a1a; cursor: pointer; text-align: left;
    transition: background 0.12s, border-color 0.12s;
  }
  .opt-btn:hover:not(:disabled) { background: #F7F5F2; border-color: #B0ABA4; }
  .opt-btn.correct { background: #EAF3DE; border-color: #639922; color: #27500A; font-weight: 500; }
  .opt-btn.wrong { background: #FCEBEB; border-color: #C84444; color: #501313; }
  .opt-btn:disabled { cursor: default; }
  .feedback {
    font-size: 14px; line-height: 1.55; padding: 12px 16px;
    border-radius: 10px; margin-top: 0.75rem; display: none;
  }
  .feedback.show { display: block; }
  .feedback.ok { background: #EAF3DE; color: #27500A; border: 1px solid #C0DD97; }
  .feedback.no { background: #FCEBEB; color: #501313; border: 1px solid #F7C1C1; }
  .next-btn {
    margin-top: 1rem; padding: 11px 28px;
    background: #E25A30; color: #fff; border: none;
    border-radius: 10px; font-size: 15px; font-weight: 500;
    cursor: pointer; display: none; transition: background 0.15s;
  }
  .next-btn.show { display: inline-block; }
  .next-btn:hover { background: #C84A22; }
  .score-screen { text-align: center; padding: 2.5rem 1rem; }
  .score-ring { width: 110px; height: 110px; margin: 0 auto 1.25rem; }
  .score-ring svg { width: 100%; height: 100%; }
  #ring-arc { transition: stroke-dashoffset 1s ease; }
  .score-big { font-size: 62px; font-weight: 700; color: #E25A30; line-height: 1; }
  .score-label { font-size: 16px; color: #777; margin-top: 6px; }
  .score-msg { font-size: 20px; color: #1a1a1a; margin-top: 1.25rem; font-weight: 600; }
  .score-sub { font-size: 15px; color: #666; margin-top: 10px; line-height: 1.6; max-width: 360px; margin-left: auto; margin-right: auto; }
  .restart-btn {
    margin-top: 1.75rem; padding: 13px 36px;
    background: #E25A30; color: #fff; border: none;
    border-radius: 10px; font-size: 15px; font-weight: 500; cursor: pointer;
    transition: background 0.15s;
  }
  .restart-btn:hover { background: #C84A22; }
  @media(max-width:480px){
    .question-text { font-size: 16px; }
    .opt-btn { font-size: 14px; padding: 11px 14px; }
  }
</style>
</head>
<body>
<div id="quiz-wrap">
  <div class="brand">
    <div class="brand-logo">
      <svg viewBox="0 0 24 24"><path d="M12 2C6.48 2 2 6.48 2 12s4.48 10 10 10 10-4.48 10-10S17.52 2 12 2zm-1 14H9V8h2v8zm4 0h-2V8h2v8z"/></svg>
    </div>
    <span class="brand-name">TAMTRON TRIVIA</span>
  </div>

  <div id="quiz-area">
    <div class="q-header">
      <span class="q-count" id="q-count">Question 1 of 10</span>
      <span class="score-count" id="score-count">Score: 0</span>
    </div>
    <div class="progress-bar"><div class="progress-fill" id="progress" style="width:10%"></div></div>
    <div class="question-card">
      <div class="question-text" id="q-text"></div>
      <div class="options" id="options"></div>
    </div>
    <div class="feedback" id="feedback"></div>
    <button class="next-btn" id="next-btn">Next question &rarr;</button>
  </div>

  <div id="score-area" style="display:none">
    <div class="score-screen">
      <div class="score-ring">
        <svg viewBox="0 0 100 100">
          <circle cx="50" cy="50" r="42" fill="none" stroke="#E8E4DF" stroke-width="9"/>
          <circle cx="50" cy="50" r="42" fill="none" stroke="#E25A30" stroke-width="9"
            stroke-linecap="round" id="ring-arc"
            stroke-dasharray="263.9" stroke-dashoffset="263.9"
            transform="rotate(-90 50 50)"/>
        </svg>
      </div>
      <div class="score-big" id="final-score"></div>
      <div class="score-label">out of 10</div>
      <div class="score-msg" id="score-msg"></div>
      <div class="score-sub" id="score-sub"></div>
      <button class="restart-btn" onclick="startQuiz()">Play again</button>
    </div>
  </div>
</div>

<script>
const questions = [
  {
    q: "In what year was Tamtron founded?",
    opts: ["1958", "1972", "1981", "1995"],
    a: 1,
    fact: "Tamtron was born in 1972 — right when electronics were revolutionising industry!"
  },
  {
    q: "In which Finnish city was Tamtron originally founded?",
    opts: ["Helsinki", "Oulu", "Turku", "Tampere"],
    a: 3,
    fact: "Tampere is Tamtron's hometown — and the 'Tam' in the name is a hint!"
  },
  {
    q: "Who founded Tamtron?",
    opts: ["Mikko and Anu Keskinen", "Pekka and Tytti Kysk", "Pentti and Liisa Asikainen", "Jari and Sari Mäkinen"],
    a: 1,
    fact: "Pekka and Tytti Kysk took the plunge — without knowing much about weighing at all!"
  },
  {
    q: "What were Tamtron's very first products?",
    opts: ["Truck scales", "Floor scales", "Crane and forklift scales", "Railway scales"],
    a: 2,
    fact: "Crane and forklift scales were the original innovation — weigh while you move!"
  },
  {
    q: "To approximately how many countries does Tamtron export its products?",
    opts: ["Over 20", "Over 50", "Over 100", "Over 150"],
    a: 1,
    fact: "Tamtron ships to 50+ countries through a certified global partner network."
  },
  {
    q: "What is Tamtron's digital weighing service called?",
    opts: ["DigiScale", "WeighCloud", "mScales", "SmartWeigh"],
    a: 2,
    fact: "mScales is Tamtron's digital platform for managing weighing data in the cloud."
  },
  {
    q: "Which company was Tamtron's largest-ever acquisition (completed in 2023)?",
    opts: ["Lahti Precision", "Korsells Sweden", "Jydsk Vaegtfabrik", "Coolman Oy"],
    a: 0,
    fact: "Lahti Precision joined Tamtron in March 2023 — adding 100 employees overnight!"
  },
  {
    q: "Tamtron Sweden holds a dominant share of which product market?",
    opts: ["Railway scales", "Floor scales", "Wheel loader scales", "Truck scales"],
    a: 2,
    fact: "Tamtron Sweden commands 40–60% of the entire wheel loader scale market!"
  },
  {
    q: "What quality certification does Tamtron hold?",
    opts: ["ISO 14001:2015", "ISO 9001:2015", "ISO 45001:2018", "ISO 27001:2013"],
    a: 1,
    fact: "ISO 9001:2015 — proof of Tamtron's commitment to top-quality products and services."
  },
  {
    q: "In how many countries does Tamtron have subsidiaries?",
    opts: ["4 countries", "6 countries", "8 countries", "12 countries"],
    a: 2,
    fact: "Sweden, Poland, Germany, Czech Republic, Slovakia, Norway, Denmark — plus Finland!"
  }
];

let current = 0, score = 0, answered = false;

function startQuiz() {
  current = 0; score = 0; answered = false;
  document.getElementById('quiz-area').style.display = 'block';
  document.getElementById('score-area').style.display = 'none';
  showQuestion();
}

function showQuestion() {
  const q = questions[current];
  document.getElementById('q-count').textContent = `Question ${current + 1} of 10`;
  document.getElementById('score-count').textContent = `Score: ${score}`;
  document.getElementById('progress').style.width = `${((current) / 10) * 100 + 10}%`;
  document.getElementById('q-text').textContent = q.q;
  const opts = document.getElementById('options');
  opts.innerHTML = '';
  q.opts.forEach((o, i) => {
    const b = document.createElement('button');
    b.className = 'opt-btn';
    b.textContent = o;
    b.onclick = () => answer(i);
    opts.appendChild(b);
  });
  const fb = document.getElementById('feedback');
  fb.className = 'feedback';
  fb.textContent = '';
  document.getElementById('next-btn').className = 'next-btn';
  answered = false;
}

function answer(i) {
  if (answered) return;
  answered = true;
  const q = questions[current];
  const btns = document.querySelectorAll('.opt-btn');
  btns.forEach(b => b.disabled = true);
  const fb = document.getElementById('feedback');
  if (i === q.a) {
    score++;
    btns[i].classList.add('correct');
    fb.className = 'feedback ok show';
    fb.textContent = '✓ Correct! ' + q.fact;
  } else {
    btns[i].classList.add('wrong');
    btns[q.a].classList.add('correct');
    fb.className = 'feedback no show';
    fb.textContent = '✗ Not quite! ' + q.fact;
  }
  document.getElementById('score-count').textContent = `Score: ${score}`;
  document.getElementById('next-btn').className = 'next-btn show';
}

document.getElementById('next-btn').onclick = function () {
  current++;
  if (current < 10) { showQuestion(); } else { showScore(); }
};

function showScore() {
  document.getElementById('quiz-area').style.display = 'none';
  document.getElementById('score-area').style.display = 'block';
  document.getElementById('final-score').textContent = score;
  const pct = score / 10;
  const circ = 263.9;
  setTimeout(() => {
    document.getElementById('ring-arc').style.strokeDashoffset = circ - (circ * pct);
  }, 100);
  let msg, sub;
  if (score === 10) { msg = 'Perfect score!'; sub = 'You know Tamtron inside out. Are you sure you don\'t work there?'; }
  else if (score >= 8) { msg = 'Impressive!'; sub = 'You really know your Tamtron facts. A gold star for you!'; }
  else if (score >= 6) { msg = 'Nice work!'; sub = 'Solid knowledge! A little more weighing trivia and you\'ll be unstoppable.'; }
  else if (score >= 4) { msg = 'Good try!'; sub = 'Not bad! Now you know a bit more about the world of weighing.'; }
  else { msg = 'Keep learning!'; sub = 'Tamtron has 50+ years of history to explore — why not dig deeper?'; }
  document.getElementById('score-msg').textContent = msg;
  document.getElementById('score-sub').textContent = sub;
}

startQuiz();
</script>
</body>
</html>
