<!DOCTYPE html>
<html lang="en" data-theme="light">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0" />
  <title>Calorie Burn Predictor</title>
  <meta name="description" content="Starter web app for predicting calories burned using personal details and daily activity readings." />
  <link href="https://api.fontshare.com/v2/css?f[]=satoshi@400,500,700&display=swap" rel="stylesheet">
  <script src="https://cdn.jsdelivr.net/npm/chart.js"></script>
  <style>
    :root, [data-theme="light"] {
      --text-xs: clamp(0.75rem, 0.7rem + 0.25vw, 0.875rem);
      --text-sm: clamp(0.875rem, 0.8rem + 0.35vw, 1rem);
      --text-base: clamp(1rem, 0.95rem + 0.25vw, 1.125rem);
      --text-lg: clamp(1.125rem, 1rem + 0.75vw, 1.5rem);
      --text-xl: clamp(1.5rem, 1.2rem + 1.25vw, 2.25rem);
      --space-1: 0.25rem; --space-2: 0.5rem; --space-3: 0.75rem; --space-4: 1rem; --space-5: 1.25rem; --space-6: 1.5rem; --space-8: 2rem; --space-10: 2.5rem; --space-12: 3rem; --space-16: 4rem;
      --color-bg: #f7f6f2; --color-surface: #f9f8f5; --color-surface-2: #fbfbf9; --color-surface-offset: #edeae5; --color-border: #d4d1ca; --color-divider: #dcd9d5;
      --color-text: #28251d; --color-text-muted: #7a7974; --color-text-faint: #bab9b4; --color-text-inverse: #f9f8f4;
      --color-primary: #01696f; --color-primary-hover: #0c4e54; --color-success: #437a22; --color-warning: #964219; --color-blue: #006494;
      --radius-sm: 0.375rem; --radius-md: 0.5rem; --radius-lg: 0.75rem; --radius-xl: 1rem; --radius-full: 9999px;
      --shadow-sm: 0 1px 2px rgba(40,37,29,0.06); --shadow-md: 0 4px 12px rgba(40,37,29,0.08); --shadow-lg: 0 12px 32px rgba(40,37,29,0.12);
      --transition-interactive: 180ms cubic-bezier(0.16, 1, 0.3, 1);
      --font-body: 'Satoshi', Arial, sans-serif;
      --content-wide: 1200px;
    }
    [data-theme="dark"] {
      --color-bg: #171614; --color-surface: #1c1b19; --color-surface-2: #201f1d; --color-surface-offset: #22211f; --color-border: #393836; --color-divider: #262523;
      --color-text: #cdccca; --color-text-muted: #979692; --color-text-faint: #6a6965; --color-text-inverse: #171614;
      --color-primary: #4f98a3; --color-primary-hover: #227f8b; --color-success: #6daa45; --color-warning: #bb653b; --color-blue: #5591c7;
      --shadow-sm: 0 1px 2px rgba(0,0,0,0.2); --shadow-md: 0 4px 12px rgba(0,0,0,0.3); --shadow-lg: 0 12px 32px rgba(0,0,0,0.4);
    }
    *, *::before, *::after { box-sizing: border-box; margin: 0; padding: 0; }
    html, body { height: 100%; }
    body { font-family: var(--font-body); font-size: var(--text-base); background: var(--color-bg); color: var(--color-text); line-height: 1.6; }
    button, input, select { font: inherit; }
    button { cursor: pointer; border: 0; }
    a { color: inherit; }
    :focus-visible { outline: 2px solid var(--color-primary); outline-offset: 2px; }
    .app-shell { display: grid; grid-template-columns: 280px 1fr; height: 100dvh; }
    .sidebar { background: var(--color-surface); border-right: 1px solid var(--color-border); padding: var(--space-6); overflow-y: auto; }
    .brand { display: flex; gap: var(--space-3); align-items: center; margin-bottom: var(--space-8); }
    .brand svg { width: 40px; height: 40px; color: var(--color-primary); }
    .brand-text strong { display: block; font-size: var(--text-sm); }
    .brand-text span { font-size: var(--text-xs); color: var(--color-text-muted); }
    .nav-list { list-style: none; display: grid; gap: var(--space-2); }
    .nav-btn { width: 100%; text-align: left; padding: var(--space-3) var(--space-4); border-radius: var(--radius-md); background: transparent; color: var(--color-text-muted); }
    .nav-btn.active, .nav-btn:hover { background: var(--color-surface-offset); color: var(--color-text); }
    .main { overflow-y: auto; }
    .topbar { position: sticky; top: 0; z-index: 10; background: color-mix(in srgb, var(--color-bg) 88%, transparent); backdrop-filter: blur(10px); border-bottom: 1px solid var(--color-divider); padding: var(--space-4) var(--space-6); display: flex; justify-content: space-between; align-items: center; }
    .topbar-actions { display: flex; gap: var(--space-3); align-items: center; }
    .icon-btn { min-width: 44px; min-height: 44px; border-radius: var(--radius-full); background: var(--color-surface); box-shadow: var(--shadow-sm); }
    .content { max-width: var(--content-wide); margin: 0 auto; padding: var(--space-6); display: grid; gap: var(--space-6); }
    .hero { display: grid; grid-template-columns: 1.3fr 0.9fr; gap: var(--space-6); }
    .panel { background: var(--color-surface); border: 1px solid var(--color-border); border-radius: var(--radius-xl); box-shadow: var(--shadow-sm); }
    .panel-body { padding: var(--space-6); }
    .eyebrow { font-size: var(--text-xs); letter-spacing: 0.08em; text-transform: uppercase; color: var(--color-primary); font-weight: 700; }
    h1 { font-size: var(--text-xl); line-height: 1.15; margin-top: var(--space-2); }
    .lead { color: var(--color-text-muted); margin-top: var(--space-3); max-width: 58ch; }
    .hero-stats { display: grid; grid-template-columns: repeat(3, 1fr); gap: var(--space-4); margin-top: var(--space-6); }
    .stat-card { background: var(--color-surface-2); border: 1px solid var(--color-border); border-radius: var(--radius-lg); padding: var(--space-4); }
    .stat-card span { font-size: var(--text-xs); color: var(--color-text-muted); }
    .stat-card strong { font-size: var(--text-lg); display: block; margin-top: var(--space-2); font-variant-numeric: tabular-nums; }
    .grid-2 { display: grid; grid-template-columns: repeat(2, minmax(0, 1fr)); gap: var(--space-6); }
    .section-head { display: flex; justify-content: space-between; align-items: center; margin-bottom: var(--space-4); }
    .section-head h2 { font-size: var(--text-lg); }
    .muted { color: var(--color-text-muted); }
    .form-grid { display: grid; grid-template-columns: repeat(2, minmax(0, 1fr)); gap: var(--space-4); }
    .field { display: grid; gap: var(--space-2); }
    .field label { font-size: var(--text-sm); font-weight: 600; }
    .field input, .field select { min-height: 46px; border-radius: var(--radius-md); border: 1px solid var(--color-border); background: var(--color-surface-2); color: var(--color-text); padding: 0 var(--space-3); }
    .full { grid-column: 1 / -1; }
    .btn-row { display: flex; gap: var(--space-3); flex-wrap: wrap; margin-top: var(--space-4); }
    .btn { min-height: 44px; padding: 0 var(--space-4); border-radius: var(--radius-md); font-size: var(--text-sm); font-weight: 700; }
    .btn-primary { background: var(--color-primary); color: var(--color-text-inverse); }
    .btn-primary:hover { background: var(--color-primary-hover); }
    .btn-secondary { background: var(--color-surface-offset); color: var(--color-text); border: 1px solid var(--color-border); }
    .kpi-grid { display: grid; grid-template-columns: repeat(4, 1fr); gap: var(--space-4); }
    .kpi-card { padding: var(--space-4); border-radius: var(--radius-lg); background: var(--color-surface-2); border: 1px solid var(--color-border); }
    .kpi-card p { font-size: var(--text-xs); color: var(--color-text-muted); }
    .kpi-card strong { display: block; margin-top: var(--space-2); font-size: var(--text-lg); font-variant-numeric: tabular-nums; }
    .table-wrap { overflow-x: auto; }
    table { width: 100%; border-collapse: collapse; }
    th, td { padding: var(--space-3); text-align: left; border-bottom: 1px solid var(--color-divider); font-size: var(--text-sm); }
    th { color: var(--color-text-muted); font-weight: 700; }
    .badge { display: inline-flex; align-items: center; gap: var(--space-2); padding: 0.35rem 0.65rem; border-radius: var(--radius-full); background: color-mix(in srgb, var(--color-primary) 12%, var(--color-surface)); color: var(--color-primary); font-size: var(--text-xs); font-weight: 700; }
    .insight-list { list-style: none; display: grid; gap: var(--space-3); }
    .insight-list li { padding: var(--space-4); border-radius: var(--radius-lg); background: var(--color-surface-2); border: 1px solid var(--color-border); }
    .hidden { display: none !important; }
    @media (max-width: 1024px) {
      .app-shell { grid-template-columns: 88px 1fr; }
      .brand-text, .nav-btn span { display: none; }
      .nav-btn { text-align: center; }
      .hero, .grid-2, .kpi-grid { grid-template-columns: 1fr 1fr; }
    }
    @media (max-width: 768px) {
      .app-shell { grid-template-columns: 1fr; height: auto; }
      .sidebar { display: none; }
      .hero, .grid-2, .hero-stats, .kpi-grid, .form-grid { grid-template-columns: 1fr; }
      .content { padding: var(--space-4); }
      .topbar { padding: var(--space-3) var(--space-4); }
    }
  </style>
</head>
<body>
  <a href="#main-content" class="sr-only">Skip to content</a>
  <div class="app-shell">
    <aside class="sidebar" aria-label="Primary navigation">
      <div class="brand">
        <svg viewBox="0 0 64 64" fill="none" aria-label="Calorie Burn Predictor logo">
          <rect x="8" y="8" width="48" height="48" rx="16" stroke="currentColor" stroke-width="4"></rect>
          <path d="M24 39C24 30 31 24 40 24C40 33 33 40 24 40V39Z" fill="currentColor"></path>
          <circle cx="24" cy="24" r="6" fill="currentColor"></circle>
        </svg>
        <div class="brand-text">
          <strong>BurnTrack</strong>
          <span>Calorie predictor starter</span>
        </div>
      </div>
      <ul class="nav-list">
        <li><button class="nav-btn active" data-view="dashboard"><span>Dashboard</span></button></li>
        <li><button class="nav-btn" data-view="profile"><span>User profile</span></button></li>
        <li><button class="nav-btn" data-view="activity"><span>Activity log</span></button></li>
        <li><button class="nav-btn" data-view="model"><span>Prediction logic</span></button></li>
      </ul>
    </aside>

    <main class="main" id="main-content">
      <header class="topbar">
        <div>
          <strong>Calorie Burn Predictor</strong>
          <div class="muted" style="font-size: var(--text-sm);">Starter structure with profile, activity logs, estimates, and charts</div>
        </div>
        <div class="topbar-actions">
          <span class="badge">Prototype ready</span>
          <button class="icon-btn" data-theme-toggle aria-label="Switch theme">◐</button>
        </div>
      </header>

      <div class="content">
        <section class="hero" id="view-dashboard">
          <div class="panel">
            <div class="panel-body">
              <div class="eyebrow">Student project starter</div>
              <h1>Predict daily calories burned from user profile and activity readings.</h1>
              <p class="lead">This starter app captures personal details such as name, age, height, weight, and gender, then combines them with daily readings like steps, workout duration, heart rate, and activity type.</p>
              <div class="hero-stats">
                <div class="stat-card"><span>Today estimate</span><strong id="todayCalories">428 kcal</strong></div>
                <div class="stat-card"><span>Steps logged</span><strong id="todaySteps">6,420</strong></div>
                <div class="stat-card"><span>Active minutes</span><strong id="todayMinutes">52 min</strong></div>
              </div>
            </div>
          </div>
          <div class="panel">
            <div class="panel-body">
              <div class="section-head">
                <h2>Quick notes</h2>
              </div>
              <ul class="insight-list">
                <li>Use this first as a rule-based estimator with MET-style logic or step-based heuristics.</li>
                <li>Later, replace the formula with a trained regression model using historical exercise data.</li>
                <li>Store each log with the features used for prediction so you can retrain and compare future models.</li>
              </ul>
            </div>
          </div>
        </section>

        <section class="grid-2">
          <div class="panel" id="view-profile">
            <div class="panel-body">
              <div class="section-head">
                <h2>User profile</h2>
                <span class="muted">Personal details</span>
              </div>
              <form id="profileForm" class="form-grid">
                <div class="field"><label for="name">Name</label><input id="name" name="name" type="text" value="Mayur M" /></div>
                <div class="field"><label for="age">Age</label><input id="age" name="age" type="number" value="21" /></div>
                <div class="field"><label for="gender">Gender</label><select id="gender" name="gender"><option>Male</option><option>Female</option><option>Other</option></select></div>
                <div class="field"><label for="weight">Weight (kg)</label><input id="weight" name="weight" type="number" value="68" /></div>
                <div class="field"><label for="height">Height (cm)</label><input id="height" name="height" type="number" value="173" /></div>
                <div class="field"><label for="goal">Goal</label><select id="goal" name="goal"><option>Maintain fitness</option><option>Lose weight</option><option>Increase activity</option></select></div>
                <div class="full btn-row">
                  <button type="submit" class="btn btn-primary">Save profile</button>
                  <button type="button" class="btn btn-secondary" id="fillSampleProfile">Use sample values</button>
                </div>
              </form>
            </div>
          </div>

          <div class="panel" id="view-activity">
            <div class="panel-body">
              <div class="section-head">
                <h2>Daily activity</h2>
                <span class="muted">User readings</span>
              </div>
              <form id="activityForm" class="form-grid">
                <div class="field"><label for="date">Date</label><input id="date" name="date" type="date" /></div>
                <div class="field"><label for="steps">Steps walked</label><input id="steps" name="steps" type="number" value="6500" /></div>
                <div class="field"><label for="activityType">Activity type</label><select id="activityType" name="activityType"><option value="walking">Walking</option><option value="running">Running</option><option value="cycling">Cycling</option><option value="gym">Gym workout</option></select></div>
                <div class="field"><label for="duration">Duration (min)</label><input id="duration" name="duration" type="number" value="45" /></div>
                <div class="field"><label for="distance">Distance (km)</label><input id="distance" name="distance" type="number" step="0.1" value="4.8" /></div>
                <div class="field"><label for="heartRate">Avg heart rate</label><input id="heartRate" name="heartRate" type="number" value="118" /></div>
                <div class="full btn-row">
                  <button type="submit" class="btn btn-primary">Predict calories</button>
                  <button type="button" class="btn btn-secondary" id="addSampleLog">Add sample log</button>
                </div>
              </form>
            </div>
          </div>
        </section>

        <section class="panel" id="view-model">
          <div class="panel-body">
            <div class="section-head">
              <h2>Prediction summary</h2>
              <span class="muted">Rule-based starter</span>
            </div>
            <div class="kpi-grid">
              <div class="kpi-card"><p>Estimated kcal</p><strong id="predictedCalories">428</strong></div>
              <div class="kpi-card"><p>Weekly total</p><strong id="weeklyCalories">2,965</strong></div>
              <div class="kpi-card"><p>Avg daily steps</p><strong id="avgSteps">7,120</strong></div>
              <div class="kpi-card"><p>Most common activity</p><strong id="topActivity">Walking</strong></div>
            </div>
            <div class="grid-2" style="margin-top: var(--space-6);">
              <div class="panel" style="background: var(--color-surface-2);">
                <div class="panel-body">
                  <h3 style="font-size: var(--text-lg); margin-bottom: var(--space-4);">Weekly burn trend</h3>
                  <canvas id="burnChart" height="220"></canvas>
                </div>
              </div>
              <div class="panel" style="background: var(--color-surface-2);">
                <div class="panel-body">
                  <h3 style="font-size: var(--text-lg); margin-bottom: var(--space-4);">How starter logic works</h3>
                  <ul class="insight-list">
                    <li>Base score from steps using a simple calories-per-step factor.</li>
                    <li>Extra calories added using activity intensity by type and workout duration.</li>
                    <li>Weight and heart rate slightly adjust the final estimate to personalize results.</li>
                  </ul>
                </div>
              </div>
            </div>
          </div>
        </section>

        <section class="panel">
          <div class="panel-body">
            <div class="section-head">
              <h2>Recent logs</h2>
              <span class="muted">Saved in memory for demo</span>
            </div>
            <div class="table-wrap">
              <table>
                <thead>
                  <tr>
                    <th>Date</th>
                    <th>Steps</th>
                    <th>Activity</th>
                    <th>Minutes</th>
                    <th>Heart rate</th>
                    <th>Estimated kcal</th>
                  </tr>
                </thead>
                <tbody id="logTableBody"></tbody>
              </table>
            </div>
          </div>
        </section>
      </div>
    </main>
  </div>

  <script>
    const state = {
      profile: { name: 'Mayur M', age: 21, gender: 'Male', weight: 68, height: 173, goal: 'Maintain fitness' },
      logs: [
        { date: '2026-05-19', steps: 7200, activityType: 'walking', duration: 48, distance: 5.1, heartRate: 114, calories: 432 },
        { date: '2026-05-20', steps: 8100, activityType: 'running', duration: 34, distance: 5.8, heartRate: 142, calories: 568 },
        { date: '2026-05-21', steps: 6400, activityType: 'walking', duration: 40, distance: 4.4, heartRate: 110, calories: 398 },
        { date: '2026-05-22', steps: 5300, activityType: 'gym', duration: 50, distance: 0, heartRate: 126, calories: 441 },
        { date: '2026-05-23', steps: 7600, activityType: 'cycling', duration: 42, distance: 8.2, heartRate: 130, calories: 472 },
        { date: '2026-05-24', steps: 6920, activityType: 'walking', duration: 52, distance: 4.9, heartRate: 118, calories: 428 }
      ]
    };

    const activityFactors = { walking: 0.04, running: 0.07, cycling: 0.06, gym: 0.055 };

    const els = {
      profileForm: document.getElementById('profileForm'),
      activityForm: document.getElementById('activityForm'),
      logTableBody: document.getElementById('logTableBody'),
      predictedCalories: document.getElementById('predictedCalories'),
      weeklyCalories: document.getElementById('weeklyCalories'),
      avgSteps: document.getElementById('avgSteps'),
      topActivity: document.getElementById('topActivity'),
      todayCalories: document.getElementById('todayCalories'),
      todaySteps: document.getElementById('todaySteps'),
      todayMinutes: document.getElementById('todayMinutes')
    };

    const setToday = () => {
      const today = new Date().toISOString().slice(0, 10);
      document.getElementById('date').value = today;
    };

    const estimateCalories = ({ steps, duration, activityType, heartRate }) => {
      const weight = Number(state.profile.weight || 65);
      const baseFromSteps = Number(steps) * 0.035;
      const intensity = (activityFactors[activityType] || 0.04) * Number(duration) * weight;
      const hrAdjustment = heartRate ? Math.max(0, (Number(heartRate) - 95) * 0.8) : 0;
      return Math.round(baseFromSteps + intensity * 0.12 + hrAdjustment);
    };

    const renderTable = () => {
      els.logTableBody.innerHTML = state.logs.slice().reverse().map(log => `
        <tr>
          <td>${log.date}</td>
          <td>${Number(log.steps).toLocaleString()}</td>
          <td style="text-transform: capitalize;">${log.activityType}</td>
          <td>${log.duration}</td>
          <td>${log.heartRate}</td>
          <td>${log.calories}</td>
        </tr>
      `).join('');
    };

    const summarize = () => {
      const totalCalories = state.logs.reduce((sum, log) => sum + Number(log.calories), 0);
      const avgSteps = Math.round(state.logs.reduce((sum, log) => sum + Number(log.steps), 0) / state.logs.length);
      const counts = state.logs.reduce((acc, log) => { acc[log.activityType] = (acc[log.activityType] || 0) + 1; return acc; }, {});
      const topActivity = Object.entries(counts).sort((a, b) => b[1] - a[1])[0]?.[0] || 'walking';
      const latest = state.logs[state.logs.length - 1];
      els.predictedCalories.textContent = latest.calories;
      els.weeklyCalories.textContent = totalCalories.toLocaleString();
      els.avgSteps.textContent = avgSteps.toLocaleString();
      els.topActivity.textContent = topActivity[0].toUpperCase() + topActivity.slice(1);
      els.todayCalories.textContent = `${latest.calories} kcal`;
      els.todaySteps.textContent = Number(latest.steps).toLocaleString();
      els.todayMinutes.textContent = `${latest.duration} min`;
    };

    let burnChart;
    const renderChart = () => {
      const ctx = document.getElementById('burnChart');
      const labels = state.logs.map(log => log.date.slice(5));
      const data = state.logs.map(log => log.calories);
      if (burnChart) burnChart.destroy();
      burnChart = new Chart(ctx, {
        type: 'line',
        data: {
          labels,
          datasets: [{
            label: 'Calories burned',
            data,
            borderColor: getComputedStyle(document.documentElement).getPropertyValue('--color-primary').trim(),
            backgroundColor: 'rgba(1, 105, 111, 0.12)',
            tension: 0.35,
            fill: true,
            pointRadius: 4
          }]
        },
        options: {
          responsive: true,
          maintainAspectRatio: false,
          plugins: { legend: { display: false } },
          scales: {
            x: { ticks: { color: getComputedStyle(document.documentElement).getPropertyValue('--color-text-muted').trim() }, grid: { color: 'rgba(120,120,120,0.12)' } },
            y: { ticks: { color: getComputedStyle(document.documentElement).getPropertyValue('--color-text-muted').trim() }, grid: { color: 'rgba(120,120,120,0.12)' } }
          }
        }
      });
    };

    const syncProfileFromForm = () => {
      const formData = new FormData(els.profileForm);
      state.profile = Object.fromEntries(formData.entries());
    };

    els.profileForm.addEventListener('submit', (e) => {
      e.preventDefault();
      syncProfileFromForm();
      alert('Profile saved in app state. Connect this to your backend API later.');
    });

    els.activityForm.addEventListener('submit', (e) => {
      e.preventDefault();
      const fd = new FormData(els.activityForm);
      const log = Object.fromEntries(fd.entries());
      log.calories = estimateCalories(log);
      state.logs.push(log);
      renderTable();
      summarize();
      renderChart();
    });

    document.getElementById('fillSampleProfile').addEventListener('click', () => {
      document.getElementById('name').value = 'Mayur M';
      document.getElementById('age').value = 21;
      document.getElementById('weight').value = 68;
      document.getElementById('height').value = 173;
    });

    document.getElementById('addSampleLog').addEventListener('click', () => {
      document.getElementById('steps').value = 8400;
      document.getElementById('duration').value = 55;
      document.getElementById('distance').value = 6.1;
      document.getElementById('heartRate').value = 124;
      document.getElementById('activityType').value = 'walking';
    });

    document.querySelectorAll('.nav-btn').forEach(btn => {
      btn.addEventListener('click', () => {
        document.querySelectorAll('.nav-btn').forEach(item => item.classList.remove('active'));
        btn.classList.add('active');
        const target = btn.dataset.view;
        const section = document.getElementById(`view-${target}`);
        if (section) section.scrollIntoView({ behavior: 'smooth', block: 'start' });
      });
    });

    (function(){
      const toggle = document.querySelector('[data-theme-toggle]');
      const root = document.documentElement;
      let mode = window.matchMedia('(prefers-color-scheme: dark)').matches ? 'dark' : 'light';
      root.setAttribute('data-theme', mode);
      toggle.addEventListener('click', () => {
        mode = mode === 'dark' ? 'light' : 'dark';
        root.setAttribute('data-theme', mode);
        renderChart();
      });
    })();

    setToday();
    renderTable();
    summarize();
    renderChart();
  </script>
</body>
</html>
