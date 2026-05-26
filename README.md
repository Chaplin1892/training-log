<!DOCTYPE html>
<html lang="ko">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0, viewport-fit=cover">
<meta name="apple-mobile-web-app-capable" content="yes">
<meta name="apple-mobile-web-app-status-bar-style" content="black-translucent">
<meta name="apple-mobile-web-app-title" content="Training Log">
<meta name="theme-color" content="#0e0e0f">
<link rel="manifest" href="manifest.json">
<title>Training Log</title>
<link href="https://fonts.googleapis.com/css2?family=DM+Serif+Display:ital@0;1&family=DM+Mono:wght@300;400;500&display=swap" rel="stylesheet">
<style>
  :root {
    --bg: #0e0e0f;
    --surface: #161618;
    --surface2: #1e1e22;
    --border: #2a2a2d;
    --text-primary: #f0ede8;
    --text-muted: #6b6b70;
    --text-dim: #3a3a3f;
    --pt: #e8c547;
    --pt-bg: #2a2410;
    --solo-gym: #5bc8af;
    --solo-gym-bg: #0e2420;
    --golf-lesson: #e87c5a;
    --golf-lesson-bg: #2a1610;
    --golf-solo: #9b7de8;
    --golf-solo-bg: #1a1228;
    --safe-top: env(safe-area-inset-top, 0px);
    --safe-bottom: env(safe-area-inset-bottom, 0px);
  }

  * { margin: 0; padding: 0; box-sizing: border-box; -webkit-tap-highlight-color: transparent; }

  html, body {
    height: 100%;
    background: var(--bg);
    color: var(--text-primary);
    font-family: 'DM Mono', monospace;
    overscroll-behavior: none;
  }

  .app {
    display: flex;
    flex-direction: column;
    height: 100dvh;
    max-width: 900px;
    margin: 0 auto;
  }

  /* ── TOP NAV ── */
  .top-nav {
    flex-shrink: 0;
    display: flex;
    align-items: center;
    justify-content: space-between;
    padding: calc(var(--safe-top) + 14px) 20px 14px;
    border-bottom: 1px solid var(--border);
    background: var(--bg);
    position: sticky;
    top: 0;
    z-index: 50;
  }

  .app-title {
    font-family: 'DM Serif Display', serif;
    font-size: 20px;
    letter-spacing: -0.5px;
    color: var(--text-primary);
  }

  .month-tabs {
    display: flex;
    gap: 2px;
    background: var(--surface);
    border: 1px solid var(--border);
    border-radius: 8px;
    padding: 3px;
    overflow-x: auto;
    max-width: 72vw;
    scrollbar-width: none;
  }
  .month-tabs::-webkit-scrollbar { display: none; }

  .tab-btn {
    font-family: 'DM Mono', monospace;
    font-size: 11px;
    letter-spacing: 1px;
    text-transform: uppercase;
    padding: 6px 10px;
    border-radius: 6px;
    border: none;
    background: transparent;
    color: var(--text-muted);
    cursor: pointer;
    transition: all 0.15s;
    white-space: nowrap;
    flex-shrink: 0;
  }

  .tab-btn.active {
    background: var(--surface2);
    color: var(--text-primary);
    border: 1px solid var(--border);
  }

  /* ── CONTENT ── */
  .content {
    flex: 1;
    overflow-y: auto;
    padding: 20px 16px calc(var(--safe-bottom) + 20px);
  }

  .month-panel { display: none; }
  .month-panel.active { display: block; }

  .month-header {
    display: flex;
    align-items: flex-end;
    justify-content: space-between;
    margin-bottom: 20px;
  }

  .month-label {
    font-family: 'DM Serif Display', serif;
    font-size: clamp(40px, 10vw, 64px);
    line-height: 0.9;
    letter-spacing: -2px;
  }

  .month-label small {
    display: block;
    font-family: 'DM Mono', monospace;
    font-size: 11px;
    font-weight: 300;
    letter-spacing: 4px;
    color: var(--text-muted);
    margin-bottom: 6px;
  }

  .legend {
    display: flex;
    flex-direction: column;
    gap: 6px;
    align-items: flex-end;
  }

  .legend-item {
    display: flex;
    align-items: center;
    gap: 7px;
    font-size: 10px;
    letter-spacing: 1px;
    text-transform: uppercase;
    color: var(--text-muted);
  }

  .legend-dot {
    width: 22px;
    height: 22px;
    border-radius: 5px;
    display: flex;
    align-items: center;
    justify-content: center;
    font-size: 12px;
  }

  /* ── CALENDAR ── */
  .calendar {
    display: grid;
    grid-template-columns: repeat(7, 1fr);
    gap: 2px;
    margin-bottom: 20px;
  }

  .day-header {
    text-align: center;
    font-size: 9px;
    letter-spacing: 1.5px;
    text-transform: uppercase;
    color: var(--text-dim);
    padding: 8px 0;
  }
  .day-header.we { color: #444; }

  .day-cell {
    background: var(--surface);
    border: 1px solid var(--border);
    border-radius: 4px;
    min-height: 82px;
    padding: 8px 6px 6px;
    position: relative;
    cursor: pointer;
    transition: border-color 0.12s;
    -webkit-user-select: none;
    user-select: none;
  }
  .day-cell:active { opacity: 0.8; }
  .day-cell.empty { background: transparent; border-color: transparent; pointer-events: none; }
  .day-cell.today { border-color: #555; }
  .day-cell.weekend .day-num { color: #444; }

  .day-num {
    font-size: 11px;
    font-weight: 500;
    color: var(--text-dim);
    line-height: 1;
    margin-bottom: 6px;
    display: flex;
    align-items: center;
    justify-content: flex-start;
  }

  .today .day-num-inner {
    background: var(--text-primary);
    color: var(--bg);
    width: 20px;
    height: 20px;
    border-radius: 50%;
    display: flex;
    align-items: center;
    justify-content: center;
  }

  .badges {
    display: flex;
    flex-wrap: wrap;
    gap: 3px;
  }

  .badge {
    width: 24px;
    height: 24px;
    border-radius: 6px;
    display: flex;
    align-items: center;
    justify-content: center;
    font-size: 12px;
  }

  .badge.pt          { background: var(--pt-bg);          border: 1px solid var(--pt); }
  .badge.solo-gym    { background: var(--solo-gym-bg);    border: 1px solid var(--solo-gym); }
  .badge.golf-lesson { background: var(--golf-lesson-bg); border: 1px solid var(--golf-lesson); }
  .badge.golf-solo   { background: var(--golf-solo-bg);   border: 1px solid var(--golf-solo); }

  /* ── STATS ── */
  .stats {
    display: grid;
    grid-template-columns: repeat(4, 1fr);
    gap: 2px;
    margin-bottom: 8px;
  }

  .stat-item {
    background: var(--surface);
    border: 1px solid var(--border);
    border-radius: 4px;
    padding: 14px 8px;
    text-align: center;
  }

  .stat-num {
    font-family: 'DM Serif Display', serif;
    font-size: 28px;
    line-height: 1;
    margin-bottom: 4px;
  }

  .stat-label {
    font-size: 8px;
    letter-spacing: 1.5px;
    text-transform: uppercase;
    color: var(--text-muted);
  }

  .stat-item.s-pt .stat-num          { color: var(--pt); }
  .stat-item.s-solo-gym .stat-num    { color: var(--solo-gym); }
  .stat-item.s-golf-lesson .stat-num { color: var(--golf-lesson); }
  .stat-item.s-golf-solo .stat-num   { color: var(--golf-solo); }

  .total-bar {
    background: var(--surface);
    border: 1px solid var(--border);
    border-radius: 4px;
    padding: 12px 16px;
    display: flex;
    align-items: center;
    justify-content: space-between;
    font-size: 10px;
    letter-spacing: 1.5px;
    text-transform: uppercase;
    color: var(--text-muted);
  }

  .total-count {
    font-family: 'DM Serif Display', serif;
    font-size: 20px;
    color: var(--text-primary);
    letter-spacing: 0;
  }

  /* ── MODAL ── */
  .overlay {
    position: fixed;
    inset: 0;
    background: rgba(0,0,0,0.72);
    backdrop-filter: blur(6px);
    -webkit-backdrop-filter: blur(6px);
    display: flex;
    align-items: flex-end;
    justify-content: center;
    z-index: 200;
    opacity: 0;
    pointer-events: none;
    transition: opacity 0.2s;
    padding-bottom: var(--safe-bottom);
  }

  .overlay.open { opacity: 1; pointer-events: all; }

  .sheet {
    background: #1c1c1f;
    border: 1px solid var(--border);
    border-radius: 16px 16px 0 0;
    padding: 20px 20px calc(20px + var(--safe-bottom));
    width: 100%;
    max-width: 900px;
    transform: translateY(20px);
    transition: transform 0.22s cubic-bezier(0.32, 0.72, 0, 1);
  }

  .overlay.open .sheet { transform: translateY(0); }

  .sheet-handle {
    width: 36px;
    height: 4px;
    background: var(--border);
    border-radius: 2px;
    margin: 0 auto 20px;
  }

  .sheet-title {
    font-family: 'DM Serif Display', serif;
    font-size: 22px;
    margin-bottom: 16px;
  }

  .sheet-date-acts {
    display: flex;
    flex-wrap: wrap;
    gap: 6px;
    margin-bottom: 16px;
    min-height: 32px;
  }

  .act-chip {
    display: flex;
    align-items: center;
    gap: 5px;
    padding: 4px 10px 4px 6px;
    border-radius: 20px;
    font-size: 11px;
    letter-spacing: 0.5px;
    cursor: pointer;
    transition: opacity 0.1s;
  }
  .act-chip:active { opacity: 0.6; }
  .act-chip .chip-x { font-size: 10px; color: rgba(255,255,255,0.4); margin-left: 2px; }

  .act-chip.pt          { background: var(--pt-bg); border: 1px solid var(--pt); color: var(--pt); }
  .act-chip.solo-gym    { background: var(--solo-gym-bg); border: 1px solid var(--solo-gym); color: var(--solo-gym); }
  .act-chip.golf-lesson { background: var(--golf-lesson-bg); border: 1px solid var(--golf-lesson); color: var(--golf-lesson); }
  .act-chip.golf-solo   { background: var(--golf-solo-bg); border: 1px solid var(--golf-solo); color: var(--golf-solo); }

  .sheet-divider { height: 1px; background: var(--border); margin-bottom: 14px; }

  .sheet-options {
    display: grid;
    grid-template-columns: 1fr 1fr;
    gap: 8px;
  }

  .opt-btn {
    display: flex;
    align-items: center;
    gap: 10px;
    padding: 14px 14px;
    border-radius: 10px;
    border: 1px solid var(--border);
    background: var(--surface);
    font-family: 'DM Mono', monospace;
    font-size: 12px;
    letter-spacing: 0.5px;
    color: var(--text-muted);
    cursor: pointer;
    transition: all 0.12s;
  }

  .opt-btn .opt-icon { font-size: 20px; }
  .opt-btn:active { opacity: 0.7; }
  .opt-btn.opt-pt:active          { border-color: var(--pt); color: var(--pt); }
  .opt-btn.opt-solo-gym:active    { border-color: var(--solo-gym); color: var(--solo-gym); }
  .opt-btn.opt-golf-lesson:active { border-color: var(--golf-lesson); color: var(--golf-lesson); }
  .opt-btn.opt-golf-solo:active   { border-color: var(--golf-solo); color: var(--golf-solo); }

  .sheet-cancel {
    display: block;
    width: 100%;
    margin-top: 10px;
    padding: 12px;
    border-radius: 10px;
    border: 1px solid var(--border);
    background: transparent;
    font-family: 'DM Mono', monospace;
    font-size: 11px;
    letter-spacing: 2px;
    text-transform: uppercase;
    color: var(--text-muted);
    cursor: pointer;
  }

  @media (min-width: 500px) {
    .day-cell { min-height: 90px; }
    .badge { width: 26px; height: 26px; font-size: 13px; }
    .overlay { align-items: center; }
    .sheet { border-radius: 16px; max-width: 420px; }
  }

  @media (min-width: 768px) {
    .content { padding: 28px 24px; }
    .day-cell { min-height: 100px; padding: 10px 8px 8px; }
  }
</style>
</head>
<body>

<div class="app">
  <nav class="top-nav">
    <div class="app-title">Training Log</div>
    <div class="month-tabs" id="month-tabs"></div>
  </nav>

  <div class="content" id="content"></div>
</div>

<!-- BOTTOM SHEET -->
<div class="overlay" id="overlay" onclick="handleOverlayClick(event)">
  <div class="sheet">
    <div class="sheet-handle"></div>
    <div class="sheet-title" id="sheet-title"></div>
    <div class="sheet-date-acts" id="sheet-acts"></div>
    <div class="sheet-divider"></div>
    <div class="sheet-options">
      <button class="opt-btn opt-pt"          onclick="addAct('pt')">          <span class="opt-icon">🏋️</span>PT 레슨</button>
      <button class="opt-btn opt-solo-gym"    onclick="addAct('solo-gym')">    <span class="opt-icon">💪</span>혼자 헬스</button>
      <button class="opt-btn opt-golf-lesson" onclick="addAct('golf-lesson')"> <span class="opt-icon">⛳</span>골프 레슨</button>
      <button class="opt-btn opt-golf-solo"   onclick="addAct('golf-solo')">   <span class="opt-icon">🏌️</span>혼자 골프</button>
    </div>
    <button class="sheet-cancel" onclick="closeSheet()">닫기</button>
  </div>
</div>

<script>
// ── CONFIG ───────────────────────────────────────────────────────
const MONTHS = [
  { key: 'apr', year: 2026, month: 3,  label: 'April',     kor: '4월',  days: 30, startDay: 3 },
  { key: 'may', year: 2026, month: 4,  label: 'May',       kor: '5월',  days: 31, startDay: 5 },
  { key: 'jun', year: 2026, month: 5,  label: 'June',      kor: '6월',  days: 30, startDay: 1 },
  { key: 'jul', year: 2026, month: 6,  label: 'July',      kor: '7월',  days: 31, startDay: 3 },
  { key: 'aug', year: 2026, month: 7,  label: 'August',    kor: '8월',  days: 31, startDay: 6 },
  { key: 'sep', year: 2026, month: 8,  label: 'September', kor: '9월',  days: 30, startDay: 2 },
  { key: 'oct', year: 2026, month: 9,  label: 'October',   kor: '10월', days: 31, startDay: 4 },
  { key: 'nov', year: 2026, month: 10, label: 'November',  kor: '11월', days: 30, startDay: 0 },
  { key: 'dec', year: 2026, month: 11, label: 'December',  kor: '12월', days: 31, startDay: 2 },
];

const ACTS = {
  'pt':          { label: 'PT 레슨',   icon: '🏋️', cls: 'pt' },
  'solo-gym':    { label: '혼자 헬스', icon: '💪', cls: 'solo-gym' },
  'golf-lesson': { label: '골프 레슨', icon: '⛳', cls: 'golf-lesson' },
  'golf-solo':   { label: '혼자 골프', icon: '🏌️', cls: 'golf-solo' },
};

const DAY_NAMES = ['SUN','MON','TUE','WED','THU','FRI','SAT'];
const STORAGE_KEY = 'training_log_2026';

// ── STATE ────────────────────────────────────────────────────────
let data = {};
MONTHS.forEach(m => { data[m.key] = {}; });
try {
  const raw = localStorage.getItem(STORAGE_KEY);
  if (raw) {
    const saved = JSON.parse(raw);
    MONTHS.forEach(m => { if (saved[m.key]) data[m.key] = saved[m.key]; });
  }
} catch(e) {}

function save() {
  try { localStorage.setItem(STORAGE_KEY, JSON.stringify(data)); } catch(e) {}
}

let activeMonth = null;
let selectedKey = null;

// ── BUILD UI ─────────────────────────────────────────────────────
function buildTabs() {
  const tabs = document.getElementById('month-tabs');
  MONTHS.forEach(m => {
    const btn = document.createElement('button');
    btn.className = 'tab-btn';
    btn.id = `tab-${m.key}`;
    btn.textContent = m.label.slice(0, 3);
    btn.onclick = () => switchMonth(m.key);
    tabs.appendChild(btn);
  });
}

function buildPanels() {
  const content = document.getElementById('content');
  MONTHS.forEach(m => {
    const panel = document.createElement('div');
    panel.className = 'month-panel';
    panel.id = `panel-${m.key}`;
    panel.innerHTML = `
      <div class="month-header">
        <div class="month-label"><small>2026</small>${m.label}</div>
        <div class="legend">
          <div class="legend-item"><div class="legend-dot badge pt">🏋️</div>PT 레슨</div>
          <div class="legend-item"><div class="legend-dot badge solo-gym">💪</div>혼자 헬스</div>
          <div class="legend-item"><div class="legend-dot badge golf-lesson">⛳</div>골프 레슨</div>
          <div class="legend-item"><div class="legend-dot badge golf-solo">🏌️</div>혼자 골프</div>
        </div>
      </div>
      <div class="calendar" id="cal-${m.key}"></div>
      <div class="stats" id="stats-${m.key}"></div>
      <div class="total-bar" id="total-${m.key}">
        <span>이번 달 총 운동</span><span class="total-count">0</span>
      </div>
    `;
    content.appendChild(panel);
  });
}

// ── CALENDAR ─────────────────────────────────────────────────────
function buildCalendar(mkey) {
  const cfg = MONTHS.find(m => m.key === mkey);
  const el = document.getElementById(`cal-${mkey}`);
  el.innerHTML = '';

  DAY_NAMES.forEach((d, i) => {
    const h = document.createElement('div');
    h.className = 'day-header' + (i === 0 || i === 6 ? ' we' : '');
    h.textContent = d;
    el.appendChild(h);
  });

  for (let i = 0; i < cfg.startDay; i++) {
    const e = document.createElement('div');
    e.className = 'day-cell empty';
    el.appendChild(e);
  }

  const today = new Date();
  const isThisMonth = today.getFullYear() === cfg.year && today.getMonth() === cfg.month;

  for (let d = 1; d <= cfg.days; d++) {
    const dow = (cfg.startDay + d - 1) % 7;
    const isWE = dow === 0 || dow === 6;
    const isToday = isThisMonth && today.getDate() === d;

    const cell = document.createElement('div');
    cell.className = 'day-cell' + (isWE ? ' weekend' : '') + (isToday ? ' today' : '');
    cell.id = `cell-${mkey}-${d}`;
    cell.onclick = () => openSheet(mkey, d);

    const numWrap = document.createElement('div');
    numWrap.className = 'day-num';
    const numInner = document.createElement('span');
    numInner.className = isToday ? 'day-num-inner' : '';
    numInner.textContent = d;
    numWrap.appendChild(numInner);
    cell.appendChild(numWrap);

    const badges = document.createElement('div');
    badges.className = 'badges';
    badges.id = `badges-${mkey}-${d}`;
    cell.appendChild(badges);

    el.appendChild(cell);
    renderBadges(mkey, d);
  }
}

function renderBadges(mkey, d) {
  const el = document.getElementById(`badges-${mkey}-${d}`);
  if (!el) return;
  el.innerHTML = '';
  const acts = data[mkey][d] || [];
  acts.forEach(type => {
    const meta = ACTS[type];
    if (!meta) return;
    const b = document.createElement('div');
    b.className = `badge ${meta.cls}`;
    b.textContent = meta.icon;
    el.appendChild(b);
  });
  renderStats(mkey);
}

// ── STATS ─────────────────────────────────────────────────────────
function renderStats(mkey) {
  const counts = { pt: 0, 'solo-gym': 0, 'golf-lesson': 0, 'golf-solo': 0 };
  Object.values(data[mkey]).forEach(acts => {
    acts.forEach(a => { if (counts[a] !== undefined) counts[a]++; });
  });
  const total = Object.values(counts).reduce((a,b) => a+b, 0);

  document.getElementById(`stats-${mkey}`).innerHTML = `
    <div class="stat-item s-pt">
      <div class="stat-num">${counts['pt']}</div>
      <div class="stat-label">PT</div>
    </div>
    <div class="stat-item s-solo-gym">
      <div class="stat-num">${counts['solo-gym']}</div>
      <div class="stat-label">혼자 헬스</div>
    </div>
    <div class="stat-item s-golf-lesson">
      <div class="stat-num">${counts['golf-lesson']}</div>
      <div class="stat-label">골프 레슨</div>
    </div>
    <div class="stat-item s-golf-solo">
      <div class="stat-num">${counts['golf-solo']}</div>
      <div class="stat-label">혼자 골프</div>
    </div>
  `;
  document.querySelector(`#total-${mkey} .total-count`).textContent = total;
}

// ── MONTH SWITCH ──────────────────────────────────────────────────
function switchMonth(mkey) {
  activeMonth = mkey;
  document.querySelectorAll('.month-panel').forEach(p => p.classList.remove('active'));
  document.querySelectorAll('.tab-btn').forEach(b => b.classList.remove('active'));
  document.getElementById(`panel-${mkey}`).classList.add('active');
  document.getElementById(`tab-${mkey}`).classList.add('active');
  document.getElementById('content').scrollTop = 0;
}

// ── SHEET ─────────────────────────────────────────────────────────
function openSheet(mkey, d) {
  selectedKey = { mkey, d };
  const cfg = MONTHS.find(m => m.key === mkey);
  document.getElementById('sheet-title').textContent = `${cfg.kor} ${d}일`;
  renderSheetActs();
  document.getElementById('overlay').classList.add('open');
}

function renderSheetActs() {
  const { mkey, d } = selectedKey;
  const el = document.getElementById('sheet-acts');
  el.innerHTML = '';
  const acts = data[mkey][d] || [];
  if (acts.length === 0) {
    el.innerHTML = `<span style="font-size:11px;color:var(--text-dim);letter-spacing:1px">기록 없음 — 아래에서 추가하세요</span>`;
    return;
  }
  acts.forEach(type => {
    const meta = ACTS[type];
    const chip = document.createElement('div');
    chip.className = `act-chip ${meta.cls}`;
    chip.innerHTML = `${meta.icon} ${meta.label} <span class="chip-x">✕</span>`;
    chip.onclick = () => removeAct(type);
    el.appendChild(chip);
  });
}

function addAct(type) {
  const { mkey, d } = selectedKey;
  if (!data[mkey][d]) data[mkey][d] = [];
  if (!data[mkey][d].includes(type)) {
    data[mkey][d].push(type);
    save();
    renderBadges(mkey, d);
    renderSheetActs();
  }
}

function removeAct(type) {
  const { mkey, d } = selectedKey;
  if (!data[mkey][d]) return;
  data[mkey][d] = data[mkey][d].filter(t => t !== type);
  save();
  renderBadges(mkey, d);
  renderSheetActs();
}

function closeSheet() {
  document.getElementById('overlay').classList.remove('open');
  selectedKey = null;
}

function handleOverlayClick(e) {
  if (e.target === document.getElementById('overlay')) closeSheet();
}

// ── INIT ──────────────────────────────────────────────────────────
buildTabs();
buildPanels();

// 현재 월 또는 가장 가까운 월로 자동 이동
const now = new Date();
const currentKey = MONTHS.find(m => m.year === now.getFullYear() && m.month === now.getMonth());
const defaultKey = currentKey ? currentKey.key : 'apr';

MONTHS.forEach(m => buildCalendar(m.key));
switchMonth(defaultKey);

// 탭 스크롤: 현재 탭이 보이도록
setTimeout(() => {
  const activeTab = document.getElementById(`tab-${defaultKey}`);
  if (activeTab) activeTab.scrollIntoView({ inline: 'center', behavior: 'smooth' });
}, 100);

document.addEventListener('keydown', e => { if (e.key === 'Escape') closeSheet(); });
</script>
</body>
</html>
-log
