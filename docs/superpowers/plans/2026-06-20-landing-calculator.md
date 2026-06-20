# Калькулятор освобождения времени — Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Transform `index.html` into an 8-question interactive quiz calculator that computes lost hours and money per year, then displays a personalized result screen with CTA to the Telegram bot `@MargaAstBrief_bot`.

**Architecture:** Single `index.html` file with three hidden `<div>` state blocks (hero / quiz / result) toggled by vanilla JS. All CSS lives in a `<style>` block in `<head>`; all JS in a `<script>` block at the bottom of `<body>`. Pure calculation functions are kept separate from DOM manipulation.

**Tech Stack:** Vanilla HTML5, CSS3 (custom properties, flexbox, CSS animations), ES6 JavaScript. Google Fonts: Montserrat + Montserrat Alternates. GitHub Pages (no build step, no dependencies).

---

## File Structure

| File | Action | Responsibility |
|---|---|---|
| `index.html` | **Rewrite** | Complete page: CSS, HTML, JS — all in one file |

Internal JS organization (within one `<script>` tag, in this order):
1. `QUESTIONS` — array of 8 quiz question objects
2. `quizState` — mutable state (currentQ, answers, multiSelected)
3. Calculation functions (pure): `calcHours`, `calcMoneyPerYear`, `formatMoney`, `calcRecovered`
4. Personalization: `DESIRE_TEXT`, `PAIN_AUTOMATION`, `PAIN_PRIORITY`, `buildPersonalText`
5. Quiz engine: `renderQuestion`, `renderDots`, `selectAnswer`, `toggleMulti`, `submitMulti`, `advanceQuiz`, `slideToNext`
6. Result: `showResult`
7. Transitions: `transitionTo`, `startQuiz`, `resetQuiz`

---

## Task 1: HTML skeleton + CSS foundation

**Files:**
- Rewrite: `index.html`

- [ ] **Step 1: Replace index.html entirely with the base skeleton**

```html
<!DOCTYPE html>
<html lang="ru">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Калькулятор освобождения времени</title>
  <link rel="preconnect" href="https://fonts.googleapis.com">
  <link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
  <link href="https://fonts.googleapis.com/css2?family=Montserrat:wght@400;500;600;700;800&family=Montserrat+Alternates:wght@700;800&display=swap" rel="stylesheet">
  <style>
    :root {
      --bg: #060d28;
      --cyan: #00c8f0;
      --lime: #a8dc4a;
      --text-dim: #7a9ec0;
      --text-dim2: #c8dca8;
      --btn-grad: linear-gradient(135deg, #8fcc20 0%, #00b8e0 100%);
      --max-w: 580px;
    }
    *, *::before, *::after { box-sizing: border-box; margin: 0; padding: 0; }
    body {
      background: var(--bg);
      color: #fff;
      font-family: 'Montserrat', sans-serif;
      min-height: 100vh;
    }
    .state { display: none; }
    .state.active {
      display: flex;
      flex-direction: column;
      min-height: 100vh;
      animation: fadeIn 400ms ease;
    }
    @keyframes fadeIn { from { opacity: 0; } to { opacity: 1; } }
  </style>
</head>
<body>
  <div id="state-hero" class="state active"></div>
  <div id="state-quiz" class="state"></div>
  <div id="state-result" class="state"></div>
  <script>
    // JS — added in later tasks
  </script>
</body>
</html>
```

- [ ] **Step 2: Open in browser, verify**

Open `index.html`. Expected: solid dark navy page, no console errors.

- [ ] **Step 3: Commit**

```bash
git add index.html
git commit -m "feat: new index.html skeleton — 3-state structure"
```

---

## Task 2: Hero section

**Files:**
- Modify: `index.html`

- [ ] **Step 1: Add hero HTML inside `#state-hero`**

Replace `<div id="state-hero" class="state active"></div>` with:

```html
<div id="state-hero" class="state active">
  <div class="hero-wrap">
    <div class="hero-label">Калькулятор освобождения времени</div>
    <h1 class="hero-title">Сколько часов ты<br>теряешь каждую неделю?</h1>
    <p class="hero-sub">2 минуты — и ты узнаешь точную цифру</p>
    <button class="btn-cta-hero" onclick="startQuiz()">Узнать →</button>
  </div>
</div>
```

- [ ] **Step 2: Add hero CSS inside `<style>`, after `@keyframes fadeIn`**

```css
/* === HERO === */
.hero-wrap {
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  text-align: center;
  flex: 1;
  padding: 48px 24px;
  max-width: var(--max-w);
  margin: 0 auto;
}
.hero-label {
  font-size: 11px;
  font-weight: 700;
  letter-spacing: .14em;
  text-transform: uppercase;
  color: var(--lime);
  margin-bottom: 24px;
}
.hero-title {
  font-family: 'Montserrat Alternates', sans-serif;
  font-size: clamp(28px, 6vw, 44px);
  font-weight: 800;
  line-height: 1.2;
  margin-bottom: 16px;
}
.hero-sub {
  font-size: 16px;
  color: var(--text-dim);
  margin-bottom: 40px;
  line-height: 1.5;
}
.btn-cta-hero {
  background: var(--btn-grad);
  color: #0a1a04;
  border: none;
  border-radius: 12px;
  padding: 16px 40px;
  font-family: 'Montserrat', sans-serif;
  font-size: 18px;
  font-weight: 600;
  cursor: pointer;
  letter-spacing: 0.01em;
  transition: transform 150ms ease, box-shadow 150ms ease;
}
.btn-cta-hero:hover {
  transform: translateY(-2px);
  box-shadow: 0 8px 24px rgba(0,200,240,0.25);
}
```

- [ ] **Step 3: Add `startQuiz` stub + `transitionTo` to JS**

Replace `// JS — added in later tasks` with:

```js
function transitionTo(id) {
  document.querySelectorAll('.state').forEach(s => s.classList.remove('active'));
  document.getElementById(id).classList.add('active');
  window.scrollTo(0, 0);
}

function startQuiz() {
  transitionTo('state-quiz');
}
```

- [ ] **Step 4: Verify in browser**

Open `index.html`. Expected: centered title on dark background, lime label above, grey subtitle below, gradient button. Click button → blank page (empty quiz state). No console errors.

- [ ] **Step 5: Commit**

```bash
git add index.html
git commit -m "feat: hero section with CTA button"
```

---

## Task 3: Quiz shell (HTML + CSS)

**Files:**
- Modify: `index.html`

- [ ] **Step 1: Add quiz HTML inside `#state-quiz`**

Replace `<div id="state-quiz" class="state"></div>` with:

```html
<div id="state-quiz" class="state">
  <div class="quiz-wrap">
    <div class="progress-dots" id="progress-dots"></div>
    <div class="question-wrap" id="question-wrap"></div>
  </div>
</div>
```

- [ ] **Step 2: Add quiz CSS inside `<style>`, after hero CSS**

```css
/* === QUIZ === */
.quiz-wrap {
  display: flex;
  flex-direction: column;
  align-items: center;
  flex: 1;
  padding: 48px 24px 40px;
  max-width: var(--max-w);
  margin: 0 auto;
  width: 100%;
}
.progress-dots {
  display: flex;
  gap: 8px;
  margin-bottom: 48px;
}
.dot {
  width: 8px;
  height: 8px;
  border-radius: 50%;
  background: rgba(255,255,255,0.15);
  transition: background 300ms ease, transform 300ms ease;
}
.dot.active { background: var(--cyan); transform: scale(1.3); }
.dot.done   { background: var(--lime); }

.question-wrap {
  width: 100%;
  flex: 1;
  display: flex;
  flex-direction: column;
  transition: transform 280ms ease, opacity 280ms ease;
}
.question-wrap.slide-out { transform: translateX(-48px); opacity: 0; pointer-events: none; }
.question-wrap.slide-in  { transform: translateX(48px);  opacity: 0; }

.question-text {
  font-size: 20px;
  font-weight: 700;
  line-height: 1.4;
  margin-bottom: 32px;
}
.hint {
  font-size: 13px;
  color: var(--text-dim);
  margin-top: -20px;
  margin-bottom: 16px;
}
.options {
  display: flex;
  flex-direction: column;
  gap: 12px;
}
.opt-btn {
  background: rgba(255,255,255,0.05);
  border: 1px solid rgba(255,255,255,0.1);
  border-radius: 12px;
  padding: 16px 20px;
  color: #fff;
  font-family: 'Montserrat', sans-serif;
  font-size: 15px;
  font-weight: 500;
  text-align: left;
  cursor: pointer;
  transition: background 150ms ease, border-color 150ms ease;
}
.opt-btn:hover  { background: rgba(0,200,240,0.08); border-color: rgba(0,200,240,0.3); }
.opt-btn.selected { background: rgba(168,220,74,0.12); border-color: var(--lime); color: var(--lime); }

.btn-next {
  margin-top: 24px;
  background: var(--btn-grad);
  color: #0a1a04;
  border: none;
  border-radius: 12px;
  padding: 14px 32px;
  font-family: 'Montserrat', sans-serif;
  font-size: 16px;
  font-weight: 600;
  cursor: pointer;
  align-self: flex-start;
  transition: opacity 200ms ease;
}
.btn-next:disabled { opacity: 0.35; cursor: default; }
```

- [ ] **Step 3: Commit**

```bash
git add index.html
git commit -m "feat: quiz shell — progress dots, question area, button styles"
```

---

## Task 4: Quiz data

**Files:**
- Modify: `index.html`

- [ ] **Step 1: Add QUESTIONS array at the top of `<script>`**

Add before all existing JS functions:

```js
const QUESTIONS = [
  {
    key: 'workHours',
    text: 'Сколько часов в неделю ты работаешь?',
    type: 'single',
    options: [
      { label: 'До 30 часов',      value: null },
      { label: '30–40 часов',      value: null },
      { label: '40–55 часов',      value: null },
      { label: 'Больше 55 часов',  value: null }
    ]
  },
  {
    key: 'messaging',
    text: 'Сколько времени уходит на переписку с клиентами и подрядчиками?',
    type: 'single',
    options: [
      { label: 'Почти нет',                value: 0 },
      { label: 'До часа в день',           value: 2 },
      { label: '1–2 часа в день',          value: 4 },
      { label: 'Больше 2 часов в день',    value: 6 }
    ]
  },
  {
    key: 'content',
    text: 'Сколько уходит на контент и соцсети?',
    type: 'single',
    options: [
      { label: 'Не веду',                     value: 0 },
      { label: 'До часа в неделю',            value: 1 },
      { label: '1–3 часа в неделю',           value: 3 },
      { label: 'Больше 3 часов в неделю',     value: 5 }
    ]
  },
  {
    key: 'admin',
    text: 'Сколько на счета, документы, планирование?',
    type: 'single',
    options: [
      { label: 'Минимум, до получаса',     value: 1 },
      { label: 'Около часа в неделю',      value: 2 },
      { label: '2–3 часа в неделю',        value: 3 },
      { label: 'Больше 3 часов в неделю',  value: 5 }
    ]
  },
  {
    key: 'switching',
    text: 'Сколько времени теряется на поиск информации и переключение между задачами?',
    type: 'single',
    options: [
      { label: 'Почти не замечаю',          value: 1 },
      { label: 'Около часа в неделю',       value: 2 },
      { label: '2–3 часа в неделю',         value: 3 },
      { label: 'Это моя главная проблема',  value: 4 }
    ]
  },
  {
    key: 'rate',
    text: 'Сколько примерно ты зарабатываешь в час?',
    type: 'single',
    options: [
      { label: 'До 1 000 р/ч',      value: 800  },
      { label: '1 000–3 000 р/ч',   value: 2000 },
      { label: '3 000–7 000 р/ч',   value: 5000 },
      { label: '7 000+ р/ч',        value: 9000 }
    ]
  },
  {
    key: 'pain',
    text: 'Что происходит из-за нехватки времени?',
    type: 'multi',
    options: [
      { label: 'Работаю в выходные',                              value: 'weekends' },
      { label: 'Не успеваю развивать бизнес',                     value: 'growth'   },
      { label: 'Теряю клиентов',                                  value: 'clients'  },
      { label: 'Выгораю',                                         value: 'burnout'  },
      { label: 'Не могу поднять цены — некогда думать об этом',  value: 'prices'   }
    ]
  },
  {
    key: 'desire',
    text: 'Если бы освободилось 10 часов в неделю — на что бы ты их направил?',
    type: 'single',
    options: [
      { label: 'Больше клиентов',     value: 'clients' },
      { label: 'Новый продукт',       value: 'product' },
      { label: 'Личное время и семья', value: 'family' },
      { label: 'Обучение и рост',     value: 'growth'  }
    ]
  }
];
```

- [ ] **Step 2: Verify data in browser console**

Open the page, open DevTools console, run:

```js
console.log(QUESTIONS.length);
// Expected: 8

console.log(QUESTIONS.find(q => q.type === 'multi').key);
// Expected: "pain"

console.log(QUESTIONS.find(q => q.key === 'rate').options.map(o => o.value));
// Expected: [800, 2000, 5000, 9000]
```

- [ ] **Step 3: Commit**

```bash
git add index.html
git commit -m "feat: add quiz questions data (8 questions)"
```

---

## Task 5: Quiz engine

**Files:**
- Modify: `index.html`

- [ ] **Step 1: Add quiz engine JS after `QUESTIONS`**

This step **replaces** the `startQuiz` stub from Task 2. Add the full engine after the QUESTIONS array, replacing the previous `startQuiz` and `transitionTo` functions entirely:

```js
// === STATE ===
const quizState = {
  currentQ: 0,
  answers: {},
  multiSelected: new Set()
};

// === TRANSITIONS ===
function transitionTo(id) {
  document.querySelectorAll('.state').forEach(s => s.classList.remove('active'));
  document.getElementById(id).classList.add('active');
  window.scrollTo(0, 0);
}

function startQuiz() {
  quizState.currentQ = 0;
  quizState.answers  = {};
  quizState.multiSelected = new Set();
  renderQuestion();
  transitionTo('state-quiz');
}

function resetQuiz() {
  transitionTo('state-hero');
}

// === QUIZ ENGINE ===
function renderDots() {
  document.getElementById('progress-dots').innerHTML =
    QUESTIONS.map((_, i) =>
      `<span class="dot ${i === quizState.currentQ ? 'active' : i < quizState.currentQ ? 'done' : ''}"></span>`
    ).join('');
}

function renderQuestion() {
  const q    = QUESTIONS[quizState.currentQ];
  const wrap = document.getElementById('question-wrap');
  renderDots();

  if (q.type === 'multi') {
    quizState.multiSelected = new Set();
    wrap.innerHTML = `
      <div class="question-text">${q.text}</div>
      <div class="hint">Можно выбрать несколько</div>
      <div class="options">
        ${q.options.map((opt, i) => `
          <button class="opt-btn" data-idx="${i}" onclick="toggleMulti(this,${i})">${opt.label}</button>
        `).join('')}
      </div>
      <button class="btn-next" id="btn-next" onclick="submitMulti()" disabled>Далее →</button>
    `;
  } else {
    wrap.innerHTML = `
      <div class="question-text">${q.text}</div>
      <div class="options">
        ${q.options.map((opt, i) => `
          <button class="opt-btn" onclick="selectAnswer(${i})">${opt.label}</button>
        `).join('')}
      </div>
    `;
  }
}

function selectAnswer(idx) {
  const q = QUESTIONS[quizState.currentQ];
  quizState.answers[q.key] = q.options[idx].value;
  advanceQuiz();
}

function toggleMulti(btn, idx) {
  if (quizState.multiSelected.has(idx)) {
    quizState.multiSelected.delete(idx);
    btn.classList.remove('selected');
  } else {
    quizState.multiSelected.add(idx);
    btn.classList.add('selected');
  }
  document.getElementById('btn-next').disabled = quizState.multiSelected.size === 0;
}

function submitMulti() {
  const q = QUESTIONS[quizState.currentQ];
  quizState.answers[q.key] = [...quizState.multiSelected].map(i => q.options[i].value);
  advanceQuiz();
}

function advanceQuiz() {
  if (quizState.currentQ < QUESTIONS.length - 1) {
    slideToNext();
  } else {
    showResult();
  }
}

function slideToNext() {
  const wrap = document.getElementById('question-wrap');
  wrap.classList.add('slide-out');
  setTimeout(() => {
    quizState.currentQ++;
    wrap.classList.remove('slide-out');
    renderQuestion();
    wrap.classList.add('slide-in');
    void wrap.offsetWidth;           // force reflow so browser sees slide-in position
    wrap.classList.remove('slide-in'); // triggers transition to default (0, opacity 1)
  }, 280);
}
```

- [ ] **Step 2: Add temporary `showResult` stub**

Add after `slideToNext` so the quiz can complete without errors:

```js
function showResult() {
  console.log('Quiz complete. Answers:', quizState.answers);
  transitionTo('state-result');
}
```

- [ ] **Step 3: Verify quiz flow manually**

Open `index.html`. Click "Узнать →". Expected:
- Q1 appears with 4 buttons, dot 1 is cyan
- Click any button → slides to Q2, dot 1 turns lime, dot 2 turns cyan
- Repeat through Q1–Q6 (single choice, auto-advance after each click)
- Q7 shows 5 multi-select buttons + disabled "Далее →"; clicking buttons selects them (lime border); "Далее →" enables after first selection
- Q8 is last single-choice; clicking → console logs "Quiz complete" + result state shows (blank)

- [ ] **Step 4: Commit**

```bash
git add index.html
git commit -m "feat: quiz engine — render, select, multi-select, slide transitions"
```

---

## Task 6: Calculation + personalization functions

**Files:**
- Modify: `index.html`

- [ ] **Step 1: Add calculation functions after `slideToNext`**

Replace the stub `showResult` and add after `slideToNext`:

```js
// === CALCULATION ===
function calcHours(answers) {
  return (answers.messaging ?? 0) +
         (answers.content   ?? 0) +
         (answers.admin     ?? 0) +
         (answers.switching ?? 0);
}

function calcMoneyPerYear(hours, rate) {
  return hours * rate * 52;
}

function formatMoney(amount) {
  if (amount >= 1000000) {
    const val = (amount / 1000000).toFixed(1).replace('.', ',');
    return val.replace(',0', '') + ' млн';
  }
  return Math.round(amount / 1000) + ' тыс.';
}

function calcRecovered(hours) {
  return {
    min: Math.max(1, Math.floor(hours * 0.55)),
    max: Math.max(2, Math.floor(hours * 0.65))
  };
}

// === PERSONALIZATION ===
const DESIRE_TEXT = {
  clients: 'Ты хочешь масштабировать клиентскую базу',
  product: 'Ты хочешь запустить новый продукт',
  family:  'Ты хочешь больше времени на себя и близких',
  growth:  'Ты хочешь инвестировать в своё развитие'
};

const PAIN_AUTOMATION = {
  weekends: 'автоматизацией рутины и планирования',
  growth:   'автоматизацией операционных задач',
  clients:  'автоматизацией переписки и работы с клиентами',
  burnout:  'автоматизацией повторяющихся задач',
  prices:   'автоматизацией административных процессов'
};

const PAIN_PRIORITY = ['clients', 'weekends', 'burnout', 'growth', 'prices'];

function buildPersonalText(answers, recovered) {
  const desire   = DESIRE_TEXT[answers.desire] ?? 'Ты хочешь освободить время';
  const painList = Array.isArray(answers.pain) ? answers.pain : [];
  const topPain  = PAIN_PRIORITY.find(p => painList.includes(p)) ?? painList[0] ?? 'weekends';
  const auto     = PAIN_AUTOMATION[topPain] ?? 'автоматизацией рутины';
  return `${desire}. С ${auto} ты вернёшь минимум ` +
    `<span style="color:#a8dc4a;font-weight:600">${recovered.min}–${recovered.max} часов</span> уже в первый месяц.`;
}
```

- [ ] **Step 2: Verify calculation functions in browser console**

Open the page (hero state is fine), open DevTools console, run:

```js
console.assert(calcHours({messaging:4, content:3, admin:2, switching:2}) === 11, 'calcHours 11h');
console.assert(calcHours({messaging:0, content:0, admin:1, switching:1}) === 2,  'calcHours 2h');
console.assert(calcHours({messaging:6, content:5, admin:5, switching:4}) === 20, 'calcHours 20h');
console.assert(formatMoney(2808000) === '2,8 млн',  'formatMoney 2.8m: ' + formatMoney(2808000));
console.assert(formatMoney(1000000) === '1 млн',    'formatMoney 1m: '   + formatMoney(1000000));
console.assert(formatMoney(840000)  === '840 тыс.', 'formatMoney 840k: ' + formatMoney(840000));
const r = calcRecovered(18); console.assert(r.min === 9  && r.max === 11, 'recovered 18h');
const r2 = calcRecovered(2); console.assert(r2.min === 1 && r2.max === 2, 'recovered 2h floor');
console.log('All assertions passed ✓');
```

Expected: all pass, last line prints.

- [ ] **Step 3: Commit**

```bash
git add index.html
git commit -m "feat: calculation and personalization functions"
```

---

## Task 7: Result screen

**Files:**
- Modify: `index.html`

- [ ] **Step 1: Add result HTML inside `#state-result`**

Replace `<div id="state-result" class="state"></div>` with:

```html
<div id="state-result" class="state">
  <div class="result-wrap">
    <div class="result-body">

      <div class="result-eyebrow">Картина твоей недели</div>

      <div class="result-top-row">
        <div class="result-top-text">
          каждую неделю ты отдаёшь задачам,<br>которые не двигают твой бизнес вперёд
        </div>
        <div class="result-big-num">
          <span class="result-hours-num" id="result-hours">0</span>
          <span class="result-hours-label">часов</span>
        </div>
      </div>

      <div class="result-divider"></div>

      <div class="result-stats-row">
        <div>
          <div class="result-stat-num cyan">
            <span id="result-hours-year">0</span> <span class="result-stat-unit">часов</span>
          </div>
          <div class="result-stat-sub">в год — почти <strong>4 месяца</strong></div>
        </div>
        <div class="result-stats-right">
          <div class="result-stat-num lime" id="result-money">—</div>
          <div class="result-stat-sub">уходит не на рост</div>
        </div>
      </div>

      <div class="result-personal-block">
        <p class="result-personal-text" id="result-personal"></p>
      </div>

      <a class="btn-cta-result" href="https://t.me/MargaAstBrief_bot" target="_blank" rel="noopener">
        Хочу эти часы обратно →
      </a>

      <div class="result-reset">
        <button onclick="resetQuiz()">Пройти ещё раз</button>
      </div>

    </div>
  </div>
</div>
```

- [ ] **Step 2: Add result CSS inside `<style>`, after quiz CSS**

```css
/* === RESULT === */
.result-wrap {
  flex: 1;
  display: flex;
  align-items: center;
  justify-content: center;
  padding: 32px 16px;
}
.result-body {
  width: 100%;
  max-width: var(--max-w);
  padding: 32px 28px;
}
.result-eyebrow {
  font-size: 12px;
  font-weight: 700;
  letter-spacing: .12em;
  text-transform: uppercase;
  color: var(--lime);
  margin-bottom: 16px;
}
.result-top-row {
  display: flex;
  align-items: flex-start;
  justify-content: space-between;
  gap: 12px;
  margin-bottom: 24px;
}
.result-top-text {
  font-size: 16px;
  font-weight: 700;
  line-height: 1.45;
  max-width: 320px;
  margin-top: 20px;
}
.result-big-num {
  flex-shrink: 0;
  text-align: right;
  white-space: nowrap;
}
.result-hours-num {
  font-size: 70px;
  font-weight: 800;
  line-height: 1;
  background: linear-gradient(135deg, #ffffff 20%, #a8dc4a 60%, #00c8f0 90%);
  -webkit-background-clip: text;
  -webkit-text-fill-color: transparent;
  background-clip: text;
}
.result-hours-label {
  font-size: 26px;
  font-weight: 700;
  color: var(--lime);
  margin-left: 4px;
}
.result-divider {
  height: 1px;
  background: linear-gradient(90deg, transparent, rgba(255,255,255,0.08), transparent);
  margin-bottom: 22px;
}
.result-stats-row {
  display: flex;
  justify-content: space-between;
  margin-bottom: 22px;
}
.result-stats-right { text-align: right; }
.result-stat-num { font-size: 35px; font-weight: 800; line-height: 1; }
.result-stat-num.cyan { color: var(--cyan); }
.result-stat-num.lime { color: var(--lime); }
.result-stat-unit { font-size: 18px; font-weight: 700; }
.result-stat-sub { font-size: 16px; color: var(--text-dim); margin-top: 4px; }
.result-stat-sub strong { color: #fff; font-weight: 600; }
.result-personal-block {
  background: rgba(168,220,74,0.07);
  border-left: 3px solid var(--lime);
  border-radius: 0 10px 10px 0;
  padding: 16px 18px;
  margin-bottom: 26px;
}
.result-personal-text { font-size: 16px; color: var(--text-dim2); line-height: 1.6; }
.btn-cta-result {
  display: block;
  background: var(--btn-grad);
  color: #0a1a04;
  border-radius: 12px;
  padding: 14px 18px;
  text-align: center;
  font-family: 'Montserrat', sans-serif;
  font-weight: 500;
  font-size: 20px;
  text-decoration: none;
  letter-spacing: 0.01em;
  transition: transform 150ms ease, box-shadow 150ms ease;
}
.btn-cta-result:hover {
  transform: translateY(-2px);
  box-shadow: 0 8px 24px rgba(0,200,240,0.25);
}
.result-reset { text-align: center; margin-top: 16px; }
.result-reset button {
  background: none;
  border: none;
  color: var(--text-dim);
  font-family: 'Montserrat', sans-serif;
  font-size: 14px;
  cursor: pointer;
  text-decoration: underline;
  text-underline-offset: 3px;
}
.result-reset button:hover { color: #fff; }
```

- [ ] **Step 3: Replace `showResult` stub with full implementation**

Remove the stub `showResult` function and add the real one after `buildPersonalText`:

```js
function showResult() {
  const hours        = calcHours(quizState.answers);
  const rate         = quizState.answers.rate ?? 2000;
  const moneyPerYear = calcMoneyPerYear(hours, rate);
  const recovered    = calcRecovered(hours);

  document.getElementById('result-hours').textContent      = hours;
  document.getElementById('result-hours-year').textContent = hours * 52;
  document.getElementById('result-personal').innerHTML     = buildPersonalText(quizState.answers, recovered);

  // Split formatted money into number + unit for styled display
  const moneyStr   = formatMoney(moneyPerYear);
  const spaceIdx   = moneyStr.lastIndexOf(' ');
  const moneyNum   = moneyStr.slice(0, spaceIdx);
  const moneyUnit  = moneyStr.slice(spaceIdx + 1);
  document.getElementById('result-money').innerHTML =
    `${moneyNum} <span class="result-stat-unit">${moneyUnit}</span>`;

  transitionTo('state-result');
}
```

- [ ] **Step 4: Verify result screen manually**

Run full quiz. Expected result screen:
- Gradient big number = sum of Q2+Q3+Q4+Q5 answers
- "часов в год" = big number × 52
- Money = formatted based on hours × rate × 52
- Green block shows personalized sentence based on Q7 + Q8
- "Хочу эти часы обратно →" links to `https://t.me/MargaAstBrief_bot`
- "Пройти ещё раз" → returns to hero

Confirm in console with a simulated run:
```js
quizState.answers = {messaging:4, content:3, admin:2, switching:2, rate:5000, pain:['clients','burnout'], desire:'family'};
showResult();
// Expected: 11 часов, 572 в год, 2,9 млн, "Ты хочешь больше времени на себя и близких. С автоматизацией переписки..."
```

- [ ] **Step 5: Commit**

```bash
git add index.html
git commit -m "feat: result screen with dynamic calculation and personalization"
```

---

## Task 8: Mobile CSS + QA

**Files:**
- Modify: `index.html`

- [ ] **Step 1: Add mobile CSS at the end of `<style>`, before `</style>`**

```css
/* === MOBILE === */
@media (max-width: 480px) {
  .quiz-wrap    { padding: 32px 16px; }
  .result-body  { padding: 24px 16px; }
  .hero-title   { font-size: 28px; }
  .btn-cta-hero { width: 100%; }
  .opt-btn      { width: 100%; }

  /* Stack result top row: big number first, text below */
  .result-top-row     { flex-direction: column-reverse; align-items: flex-start; gap: 8px; }
  .result-top-text    { margin-top: 8px; max-width: 100%; }
  .result-big-num     { text-align: left; }
  .result-hours-num   { font-size: 56px; }
  .result-hours-label { font-size: 22px; }
  .result-stat-num    { font-size: 28px; }
  .btn-cta-result     { font-size: 17px; }
}
```

- [ ] **Step 2: Test on mobile viewport**

Open DevTools (F12) → set viewport to 375×812. Verify:
- Hero: title readable, button full-width
- Quiz: all 4 answer buttons full-width, 8 dots visible in one row
- Result: "18 часов" block on top, text phrase below; stats row not overflowing; green block fits; CTA button readable

- [ ] **Step 3: End-to-end QA — max scenario**

Click through quiz selecting these answers:
- Q1: Больше 55 часов
- Q2: Больше 2 часов в день (value 6)
- Q3: Больше 3 часов в неделю (value 5)
- Q4: Больше 3 часов в неделю (value 5)
- Q5: Это моя главная проблема (value 4)
- Q6: 7 000+ р/ч (rate 9000)
- Q7: Теряю клиентов + Выгораю → Далее
- Q8: Новый продукт

Expected result: **20 часов**, **1040 в год**, **9,4 млн** (`20×9000×52=9360000`), green block: "Ты хочешь запустить новый продукт. С автоматизацией переписки и работы с клиентами ты вернёшь минимум 11–13 часов уже в первый месяц."

- [ ] **Step 4: End-to-end QA — min scenario**

Click through quiz:
- Q2: Почти нет (0)
- Q3: Не веду (0)
- Q4: Минимум (1)
- Q5: Почти не замечаю (1)
- Q6: До 1000 р/ч (800)
- Q7: Выгораю → Далее
- Q8: Личное время и семья

Expected result: **2 часа**, **104 в год**, **83 тыс.** (`2×800×52=83200`), green block: "Ты хочешь больше времени на себя и близких. С автоматизацией повторяющихся задач ты вернёшь минимум 1–2 часов уже в первый месяц."

- [ ] **Step 5: Verify "Пройти ещё раз" resets cleanly**

On result screen, click "Пройти ещё раз". Expected: hero appears. Click "Узнать →". Expected: quiz starts from Q1 with all dots grey except first (cyan). Confirm answers from previous run are not carried over.

- [ ] **Step 6: Commit and push**

```bash
git add index.html
git commit -m "feat: mobile CSS + complete QA — landing calculator done"
git push
```

- [ ] **Step 7: Verify on GitHub Pages**

Open `https://margaastar-arch.github.io/landing/`. Run the quiz end-to-end. Confirm all states work and CTA link opens `t.me/MargaAstBrief_bot`.
