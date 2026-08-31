<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8" />
  <title>NEB Grade 12 English Grammar – Daily MCQ</title>
  <meta name="viewport" content="width=device-width, initial-scale=1" />
  <link rel="stylesheet" href="css/style.css" />
</head>
<body>
  <header>
    <h1>NEB Grade 12 English Grammar Practice</h1>
    <p>Daily MCQs • PYQs • Exam-focused</p>
  </header>

  <nav>
    <a href="index.html">Home</a>
    <a href="practice.html">Topic Practice</a>
    <a href="pyq.html">PYQs (2080–2083)</a>
  </nav>

  <main>
    <section id="today">
      <h2>Today’s MCQ Set</h2>
      <div id="quiz"></div>
      <button id="submit">Submit</button>
      <div id="result"></div>
    </section>
  </main>
  <!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8" />
  <title>NEB Grammar – Topic Practice</title>
  <meta name="viewport" content="width=device-width, initial-scale=1" />
  <link rel="stylesheet" href="css/style.css" />
</head>
<body>
  <header>
    <h1>Topic-wise Practice</h1>
    <p>Choose a topic to practice 15 MCQs</p>
  </header>

  <nav>
    <a href="index.html">Home</a>
    <a href="practice.html">Topic Practice</a>
    <a href="pyq.html">PYQs</a>
  </nav>

  <main>
    <section id="topics">
      <h2>Topics</h2>
      <ul id="topic-list"></ul>
    </section>

    <section id="today">
      <h2>Practice Set</h2>
      <div id="quiz"></div>
      <button id="submit">Submit</button>
      <div id="result"></div>
    </section>
  </main>

  <footer>
    <p>Based on NEB model questions & past papers (2080–2083).</p>
  </footer>

  <script>
    const TOPICS = ["mixed"]; // start simple; expand later
    const list = document.getElementById('topic-list');
    TOPICS.forEach(t => {
      const li = document.createElement('li');
      const a = document.createElement('a');
      a.href = `?topic=${t}`;
      a.textContent = t;
      li.appendChild(a);
      list.appendChild(li);
    });
  </script>
  <script src="js/app.js"></script>
</body>
</html>
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8" />
  <title>NEB Grammar – PYQ Practice</title>
  <meta name="viewport" content="width=device-width, initial-scale=1" />
  <link rel="stylesheet" href="css/style.css" />
  <style>
    .filters { margin: 0.5rem 1rem 1rem; }
    .filters label { margin-right: 0.75rem; }
    #quiz { max-width: 800px; margin: 0 auto; }
  </style>
</head>
<body>
  <header>
    <h1>NEB PYQ Grammar Practice</h1>
    <p>Practice with previous-year style questions (2080–2083)</p>
  </header>

  <nav>
    <a href="index.html">Home</a>
    <a href="practice.html">Topic Practice</a>
    <a href="pyq.html">PYQs</a>
  </nav>

  <main>
    <section class="filters">
      <strong>Year:</strong>
      <label><input type="checkbox" value="2080" checked> 2080</label>
      <label><input type="checkbox" value="2081" checked> 2081</label>
      <label><input type="checkbox" value="2082" checked> 2082</label>
      <label><input type="checkbox" value="2083" checked> 2083</label>
      <button id="load-pyq">Load PYQ Set</button>
    </section>

    <section id="today">
      <h2>PYQ Set</h2>
      <div id="quiz"></div>
      <button id="submit">Submit</button>
      <div id="result"></div>
    </section>
  </main>

  <footer>
    <p>Based on NEB model questions & past papers (2080–2083).</p>
  </footer>

  <script>
    const TOPICS = ["mixed"]; // will expand later

    async function loadAllQuestions() {
      const all = [];
      for (const t of TOPICS) {
        const res = await fetch(`data/${t}.json`);
        if (!res.ok) continue;
        const qs = await res.json();
        all.push(...qs);
      }
      return all;
    }

    function renderQuiz(questions, containerId = 'quiz') {
      const quiz = document.getElementById(containerId);
      quiz.innerHTML = '';

      if (questions.length === 0) {
        quiz.innerHTML = '<p>No questions match the selected years.</p>';
        document.getElementById('submit').style.display = 'none';
        document.getElementById('result').textContent = '';
        return;
      }

      document.getElementById('submit').style.display = 'inline-block';

      questions.forEach((q, i) => {
        const card = document.createElement('div');
        card.className = 'q-card';
        const yearTag = q.year && q.year !== 'mixed' ? ` <span style="color:#666;font-size:0.9em">[${q.year}]</span>` : '';
        card.innerHTML = `
          <p class="q-text">${i+1}. ${q.question}${yearTag}</p>
          <div class="options">
            ${q.options.map((opt, j) => `
              <label>
                <input type="radio" name="q${i}" value="${j}"> ${opt}
              </label>
            `).join('')}
          </div>
          <div class="feedback" id="f${i}"></div>
        `;
        quiz.appendChild(card);
      });

      document.getElementById('submit').onclick = () => {
        let score = 0;
        questions.forEach((q, i) => {
          const chosen = document.querySelector(`input[name="q${i}"]:checked`);
          const fb = document.getElementById(`f${i}`);
          if (!chosen) {
            fb.textContent = 'Skipped.';
            return;
          }
          const val = +chosen.value;
          if (val === q.answer) {
            score++;
            fb.textContent = 'Correct ✓';
            fb.style.color = 'green';
          } else {
            fb.textContent = `Incorrect. Correct: ${q.options[q.answer]}`;
            fb.style.color = 'maroon';
          }
        });
        const result = document.getElementById('result');
        result.textContent = `Score: ${score} / ${questions.length}`;
      };
    }

    async function loadPYQSet() {
      const checkboxes = document.querySelectorAll('.filters input[type="checkbox"]');
      const selectedYears = Array.from(checkboxes).filter(c => c.checked).map(c => c.value);

      const all = await loadAllQuestions();
      const filtered = all.filter(q => {
        if (!q.year) return false;
        if (q.year === 'mixed') return true;
        return selectedYears.includes(q.year);
      });

      const shuffled = filtered.sort(() => 0.5 - Math.random());
      const set = shuffled.slice(0, 20);
      renderQuiz(set);
    }

    document.getElementById('load-pyq').onclick = loadPYQSet;
    window.addEventListener('DOMContentLoaded', loadPYQSet);
  </script>
</body>
</html>body { font-family: system-ui, sans-serif; margin: 0; line-height: 1.5; }
header, nav, footer { padding: 1rem; text-align: center; }
nav a { margin: 0 0.5rem; text-decoration: none; color: #005fcc; }
.q-card { border: 1px solid #ddd; border-radius: 8px; padding: 0.75rem; margin: 0.75rem 1rem; }
.q-text { font-weight: 600; margin-bottom: 0.5rem; }
.options label { display: block; margin: 0.25rem 0; }
#submit { margin: 0.5rem 1rem; padding: 0.5rem 1rem; }
#result { font-weight: 700; margin: 0 1rem 1rem; }const TOPICS = ["mixed"]; // start simple

async function loadTopicQuestions(topic) {
  const res = await fetch(`data/${topic}.json`);
  if (!res.ok) return [];
  return await res.json();
}

async function loadAllQuestions() {
  const all = [];
  for (const t of TOPICS) {
    const qs = await loadTopicQuestions(t);
    all.push(...qs);
  }
  return all;
}

function renderQuiz(questions, containerId = 'quiz') {
  const quiz = document.getElementById(containerId);
  quiz.innerHTML = '';

  if (questions.length === 0) {
    quiz.innerHTML = '<p>No questions loaded. Check data/mixed.json.</p>';
    document.getElementById('submit').style.display = 'none';
    document.getElementById('result').textContent = '';
    return;
  }

  document.getElementById('submit').style.display = 'inline-block';

  questions.forEach((q, i) => {
    const card = document.createElement('div');
    card.className = 'q-card';
    card.innerHTML = `
      <p class="q-text">${i+1}. ${q.question}</p>
      <div class="options">
        ${q.options.map((opt, j) => `
          <label>
            <input type="radio" name="q${i}" value="${j}"> ${opt}
          </label>
        `).join('')}
      </div>
      <div class="feedback" id="f${i}"></div>
    `;
    quiz.appendChild(card);
  });

  document.getElementById('submit').onclick = () => {
    let score = 0;
    questions.forEach((q, i) => {
      const chosen = document.querySelector(`input[name="q${i}"]:checked`);
      const fb = document.getElementById(`f${i}`);
      if (!chosen) {
        fb.textContent = 'Skipped.';
        return;
      }
      const val = +chosen.value;
      if (val === q.answer) {
        score++;
        fb.textContent = 'Correct ✓';
        fb.style.color = 'green';
      } else {
        fb.textContent = `Incorrect. Correct: ${q.options[q.answer]}`;
        fb.style.color = 'maroon';
      }
    });
    const result = document.getElementById('result');
    result.textContent = `Score: ${score} / ${questions.length}`;
  };
}

async function init() {
  const params = new URLSearchParams(window.location.search);
  const topic = params.get('topic');

  if (topic) {
    const qs = await loadTopicQuestions(topic);
    const shuffled = qs.sort(() => 0.5 - Math.random()).slice(0, 15);
    renderQuiz(shuffled);
  } else {
    const all = await loadAllQuestions();
    const day = Math.floor(Date.now() / 86400000);
    const start = (day * 3) % Math.max(1, all.length - 15);
    const daily = all.slice(start, start + 15);
    renderQuiz(daily);
  }
}

init();[
  {
    "id": "test_1",
    "topic": "mixed",
    "year": "mixed",
    "question": "Choose the correct sentence:",
    "options": [
      "She is good at singing.",
      "She is good in singing.",
      "She is good with singing.",
      "She is good on singing."
    ],
    "answer": 0
  },
  {
    "id": "test_2",
    "topic": "mixed",
    "year": "mixed",
    "question": "He has been working since morning.",
    "options": [
      "He has been working since morning.",
      "He has been working from morning.",
      "He has been working for morning.",
      "He has been working by morning."
    ],
    "answer": 0
  },
  {
    "id": "test_3",
    "topic": "mixed",
    "year": "mixed",
    "question": "One of the students is absent.",
    "options": [
      "One of the students is absent.",
      "One of the students are absent.",
      "One of the students were absent.",
      "One of the students have been absent."
    ],
    "answer": 0
  },
  {
    "id": "test_4",
    "topic": "mixed",
    "year": "mixed",
    "question": "Neither he nor I am guilty.",
    "options": [
      "Neither he nor I am guilty.",
      "Neither he nor I is guilty.",
      "Neither he nor I are guilty.",
      "Neither he nor I were guilty."
    ],
    "answer": 0
  },
  {
    "id": "test_5",
    "topic": "mixed",
    "year": "mixed",
    "question": "She asked me where I was going.",
    "options": [
      "She asked me where I was going.",
      "She asked me where was I going.",
      "She asked me where I am going.",
      "She asked me where am I going."
    ],
    "answer": 0
  }
]

  <footer>
    <p>Based on NEB model questions & past papers (2080–2083).</p>
  </footer>

  <script src="js/app.js"></script>
</body>
</html># neb-grammar-practic
