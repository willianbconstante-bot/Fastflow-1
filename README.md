<!DOCTYPE html>
<html lang="pt-BR">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
<meta name="theme-color" content="#0a0f1e">
<meta name="apple-mobile-web-app-capable" content="yes">
<meta name="apple-mobile-web-app-status-bar-style" content="black-translucent">
<meta name="apple-mobile-web-app-title" content="FastFlow">
<title>FastFlow — Mapa Biológico</title>
<link rel="preconnect" href="https://fonts.googleapis.com">
<link href="https://fonts.googleapis.com/css2?family=Syne:wght@400;500;600;700;800&family=DM+Sans:ital,opsz,wght@0,9..40,300;0,9..40,400;0,9..40,500;1,9..40,300&display=swap" rel="stylesheet">
<style>
  :root {
    --bg: #070b18;
    --surface: #0d1425;
    --surface2: #131929;
    --border: rgba(255,255,255,0.07);
    --text: #e8eaf0;
    --muted: #5a6380;
    --accent: #4fc3f7;
    --phase0: #7c83fd;
    --phase1: #f9a825;
    --phase2: #ff7043;
    --phase3: #66bb6a;
    --phase4: #26c6da;
    --glow: 0 0 40px rgba(79,195,247,0.15);
    --r: 20px;
  }

  * { margin: 0; padding: 0; box-sizing: border-box; -webkit-tap-highlight-color: transparent; }

  html, body {
    width: 100%; min-height: 100dvh; overflow-x: hidden;
    background: var(--bg);
    font-family: 'DM Sans', sans-serif;
    color: var(--text);
    -webkit-font-smoothing: antialiased;
  }

  /* ── SPLASH ── */
  #splash {
    position: fixed; inset: 0; z-index: 999;
    background: var(--bg);
    display: flex; flex-direction: column;
    align-items: center; justify-content: center; gap: 16px;
    transition: opacity .6s ease;
  }
  #splash .logo-big { font-family: 'Syne', sans-serif; font-size: 42px; font-weight: 800; letter-spacing: -1px; }
  #splash .logo-big span { color: var(--accent); }
  #splash p { color: var(--muted); font-size: 14px; letter-spacing: .5px; }
  #splash .pulse-ring {
    width: 80px; height: 80px; border-radius: 50%;
    border: 2px solid var(--accent);
    animation: pulseRing 1.4s ease-out infinite;
    margin-bottom: 8px;
  }
  @keyframes pulseRing { 0%{transform:scale(.8);opacity:1} 100%{transform:scale(1.6);opacity:0} }

  /* ── APP WRAPPER ── */
  #app { display: none; flex-direction: column; min-height: 100dvh; }
  #app.visible { display: flex; }

  /* ── HEADER ── */
  header {
    padding: 16px 20px 12px;
    display: flex; align-items: center; justify-content: space-between;
    border-bottom: 1px solid var(--border);
    position: sticky; top: 0; z-index: 50;
    background: rgba(7,11,24,0.92);
    backdrop-filter: blur(20px);
  }
  .logo { font-family: 'Syne', sans-serif; font-size: 22px; font-weight: 800; letter-spacing: -.5px; }
  .logo span { color: var(--accent); }
  .header-right { display: flex; gap: 10px; align-items: center; }
  .icon-btn {
    width: 38px; height: 38px; border-radius: 12px;
    background: var(--surface2); border: 1px solid var(--border);
    display: flex; align-items: center; justify-content: center;
    cursor: pointer; font-size: 18px; transition: all .2s;
    color: var(--text);
  }
  .icon-btn:active { transform: scale(.92); }

  /* ── NAV ── */
  nav {
    position: fixed; bottom: 0; left: 0; right: 0; z-index: 50;
    background: rgba(7,11,24,0.96);
    backdrop-filter: blur(20px);
    border-top: 1px solid var(--border);
    display: flex; padding: 8px 0 max(8px, env(safe-area-inset-bottom));
  }
  .nav-item {
    flex: 1; display: flex; flex-direction: column;
    align-items: center; gap: 4px;
    cursor: pointer; padding: 6px 0;
    font-size: 10px; color: var(--muted);
    letter-spacing: .4px; text-transform: uppercase;
    font-weight: 500; transition: color .2s;
  }
  .nav-item .ni { font-size: 22px; }
  .nav-item.active { color: var(--accent); }

  /* ── SCROLL AREA ── */
  .scroll-area {
    flex: 1; overflow-y: auto; padding-bottom: 90px;
    scroll-behavior: smooth;
  }
  .scroll-area::-webkit-scrollbar { display: none; }

  /* ── SCREENS ── */
  .screen { display: none; padding: 20px; }
  .screen.active { display: block; }

  /* ── TIMER SCREEN ── */
  .phase-banner {
    border-radius: var(--r); padding: 14px 18px;
    margin-bottom: 20px;
    display: flex; align-items: center; gap: 12px;
    position: relative; overflow: hidden;
    animation: fadeSlide .4s ease;
  }
  @keyframes fadeSlide { from{opacity:0;transform:translateY(-8px)} to{opacity:1;transform:translateY(0)} }
  .phase-banner::before {
    content: ''; position: absolute; inset: 0;
    background: inherit; filter: blur(30px); opacity: .3; z-index: 0;
  }
  .phase-banner > * { position: relative; z-index: 1; }
  .phase-icon { font-size: 32px; }
  .phase-info h3 { font-family: 'Syne', sans-serif; font-size: 15px; font-weight: 700; }
  .phase-info p { font-size: 12px; opacity: .8; margin-top: 2px; line-height: 1.4; }

  /* RING */
  .ring-wrap {
    display: flex; flex-direction: column;
    align-items: center; margin: 8px 0 24px;
    gap: 16px;
  }
  .ring-container {
    position: relative; width: 220px; height: 220px;
  }
  .ring-svg { width: 100%; height: 100%; transform: rotate(-90deg); }
  .ring-track { fill: none; stroke: var(--surface2); stroke-width: 12; }
  .ring-fill {
    fill: none; stroke-width: 12;
    stroke-linecap: round;
    stroke-dasharray: 628;
    stroke-dashoffset: 628;
    transition: stroke-dashoffset .6s ease, stroke .6s ease;
    filter: drop-shadow(0 0 8px currentColor);
  }
  .ring-center {
    position: absolute; inset: 0;
    display: flex; flex-direction: column;
    align-items: center; justify-content: center;
    gap: 2px;
  }
  .timer-display {
    font-family: 'Syne', sans-serif;
    font-size: 42px; font-weight: 800;
    letter-spacing: -2px; line-height: 1;
  }
  .timer-label { font-size: 11px; color: var(--muted); letter-spacing: .8px; text-transform: uppercase; }
  .timer-pct { font-size: 13px; color: var(--accent); font-weight: 600; margin-top: 2px; }

  /* STATS ROW */
  .stats-row {
    display: grid; grid-template-columns: repeat(3, 1fr);
    gap: 10px; margin-bottom: 20px;
  }
  .stat-card {
    background: var(--surface); border: 1px solid var(--border);
    border-radius: 16px; padding: 14px 12px;
    display: flex; flex-direction: column; gap: 4px;
    align-items: center; text-align: center;
  }
  .stat-card .sv { font-family: 'Syne', sans-serif; font-size: 20px; font-weight: 700; }
  .stat-card .sl { font-size: 10px; color: var(--muted); letter-spacing: .4px; text-transform: uppercase; }

  /* CTAs */
  .cta-row { display: flex; gap: 12px; margin-bottom: 20px; }
  .btn-primary {
    flex: 1; padding: 16px;
    border-radius: 16px; border: none;
    font-family: 'Syne', sans-serif;
    font-size: 15px; font-weight: 700;
    cursor: pointer; transition: all .2s;
    letter-spacing: .2px;
  }
  .btn-primary:active { transform: scale(.97); }
  .btn-start { background: linear-gradient(135deg, #4fc3f7, #0288d1); color: #fff; box-shadow: 0 4px 20px rgba(79,195,247,.3); }
  .btn-stop { background: linear-gradient(135deg, #ef5350, #b71c1c); color: #fff; box-shadow: 0 4px 20px rgba(239,83,80,.3); }
  .btn-ghost {
    padding: 16px 18px; border-radius: 16px;
    background: var(--surface); border: 1px solid var(--border);
    color: var(--text); font-family: 'Syne', sans-serif;
    font-size: 14px; font-weight: 600; cursor: pointer;
    transition: all .2s;
  }
  .btn-ghost:active { transform: scale(.97); }

  /* TIMELINE */
  .section-title {
    font-family: 'Syne', sans-serif; font-size: 13px;
    font-weight: 700; letter-spacing: .8px;
    text-transform: uppercase; color: var(--muted);
    margin-bottom: 12px;
  }
  .timeline { display: flex; flex-direction: column; gap: 0; }
  .tl-item {
    display: flex; gap: 14px; align-items: stretch;
    position: relative;
  }
  .tl-line {
    display: flex; flex-direction: column;
    align-items: center; width: 32px; flex-shrink: 0;
  }
  .tl-dot {
    width: 14px; height: 14px; border-radius: 50%;
    border: 2px solid; flex-shrink: 0; z-index: 1;
    transition: all .3s;
  }
  .tl-connector {
    flex: 1; width: 2px;
    background: var(--border); margin: 2px 0;
  }
  .tl-item:last-child .tl-connector { display: none; }
  .tl-content {
    background: var(--surface); border: 1px solid var(--border);
    border-radius: 14px; padding: 14px;
    margin-bottom: 8px; flex: 1;
    transition: all .3s;
  }
  .tl-content.active-phase {
    border-color: currentColor;
    box-shadow: 0 0 20px rgba(79,195,247,.1);
  }
  .tl-content h4 { font-family: 'Syne', sans-serif; font-size: 13px; font-weight: 700; margin-bottom: 4px; }
  .tl-content p { font-size: 12px; color: var(--muted); line-height: 1.5; }
  .tl-content .tl-badge {
    display: inline-block; padding: 3px 8px;
    border-radius: 20px; font-size: 10px; font-weight: 600;
    letter-spacing: .4px; text-transform: uppercase;
    margin-top: 8px; background: rgba(255,255,255,.07);
  }

  /* SOS */
  .sos-card {
    background: linear-gradient(135deg, rgba(239,83,80,.12), rgba(183,28,28,.08));
    border: 1px solid rgba(239,83,80,.3);
    border-radius: var(--r); padding: 18px;
    margin-bottom: 20px; cursor: pointer;
    transition: all .2s; text-align: center;
  }
  .sos-card:active { transform: scale(.98); }
  .sos-card h3 { font-family: 'Syne', sans-serif; font-size: 15px; font-weight: 700; color: #ef5350; margin-bottom: 4px; }
  .sos-card p { font-size: 12px; color: var(--muted); }

  /* ── PROTOCOLS SCREEN ── */
  .protocols-grid { display: flex; flex-direction: column; gap: 12px; }
  .proto-card {
    background: var(--surface); border: 1px solid var(--border);
    border-radius: var(--r); padding: 18px;
    cursor: pointer; transition: all .2s;
    display: flex; align-items: center; gap: 14px;
  }
  .proto-card:active { transform: scale(.98); }
  .proto-card.selected { border-color: var(--accent); background: rgba(79,195,247,.06); }
  .proto-icon {
    width: 52px; height: 52px; border-radius: 16px;
    display: flex; align-items: center; justify-content: center;
    font-size: 24px; flex-shrink: 0;
  }
  .proto-info { flex: 1; }
  .proto-info h3 { font-family: 'Syne', sans-serif; font-size: 16px; font-weight: 700; margin-bottom: 2px; }
  .proto-info p { font-size: 12px; color: var(--muted); line-height: 1.4; }
  .proto-tag {
    padding: 4px 10px; border-radius: 20px;
    font-size: 10px; font-weight: 600;
    letter-spacing: .3px; text-transform: uppercase;
  }
  .tag-beginner { background: rgba(102,187,106,.15); color: #66bb6a; }
  .tag-intermediate { background: rgba(249,168,37,.15); color: #f9a825; }
  .tag-advanced { background: rgba(239,83,80,.15); color: #ef5350; }

  /* ── HYDRATION SCREEN ── */
  .hydro-hero {
    background: var(--surface); border: 1px solid var(--border);
    border-radius: var(--r); padding: 24px;
    text-align: center; margin-bottom: 20px;
  }
  .water-glass {
    width: 70px; height: 90px; margin: 0 auto 16px;
    border: 3px solid rgba(79,195,247,.4);
    border-radius: 6px 6px 12px 12px;
    position: relative; overflow: hidden;
    background: rgba(79,195,247,.04);
  }
  .water-fill {
    position: absolute; bottom: 0; left: 0; right: 0;
    background: linear-gradient(180deg, rgba(79,195,247,.5), rgba(2,136,209,.7));
    transition: height 1s cubic-bezier(.4,0,.2,1);
    border-radius: 0 0 9px 9px;
  }
  .water-pct { font-family: 'Syne', sans-serif; font-size: 32px; font-weight: 800; color: var(--accent); margin-bottom: 4px; }
  .water-label { font-size: 13px; color: var(--muted); }
  .water-btns { display: flex; gap: 10px; margin-top: 16px; justify-content: center; }
  .w-btn {
    padding: 10px 20px; border-radius: 12px;
    background: rgba(79,195,247,.12); border: 1px solid rgba(79,195,247,.2);
    color: var(--accent); font-family: 'Syne', sans-serif;
    font-size: 13px; font-weight: 700; cursor: pointer;
    transition: all .2s;
  }
  .w-btn:active { transform: scale(.95); background: rgba(79,195,247,.22); }

  .drink-list { display: flex; flex-direction: column; gap: 8px; }
  .drink-item {
    background: var(--surface); border: 1px solid var(--border);
    border-radius: 14px; padding: 14px 16px;
    display: flex; align-items: center; gap: 12px;
    cursor: pointer; transition: all .2s;
  }
  .drink-item:active { transform: scale(.98); }
  .drink-item .di { font-size: 24px; }
  .drink-info { flex: 1; }
  .drink-info h4 { font-size: 14px; font-weight: 500; }
  .drink-info p { font-size: 11px; color: var(--muted); margin-top: 2px; }
  .drink-add { color: var(--accent); font-size: 22px; font-weight: 300; }

  /* ── DIARY SCREEN ── */
  .diary-entry {
    background: var(--surface); border: 1px solid var(--border);
    border-radius: var(--r); padding: 20px; margin-bottom: 16px;
  }
  .diary-entry h3 { font-family: 'Syne', sans-serif; font-size: 15px; font-weight: 700; margin-bottom: 16px; }
  .emoji-row { display: flex; gap: 10px; flex-wrap: wrap; }
  .emoji-opt {
    flex: 1; min-width: 52px;
    padding: 10px 8px; border-radius: 12px;
    background: var(--surface2); border: 1px solid var(--border);
    text-align: center; font-size: 22px; cursor: pointer;
    transition: all .2s;
  }
  .emoji-opt.selected { border-color: var(--accent); background: rgba(79,195,247,.1); transform: scale(1.05); }
  .emoji-opt span { display: block; font-size: 9px; color: var(--muted); margin-top: 4px; letter-spacing: .3px; }
  .save-entry-btn {
    width: 100%; padding: 16px; border-radius: 16px;
    background: linear-gradient(135deg, rgba(79,195,247,.15), rgba(2,136,209,.1));
    border: 1px solid rgba(79,195,247,.3); color: var(--accent);
    font-family: 'Syne', sans-serif; font-size: 15px; font-weight: 700;
    cursor: pointer; transition: all .2s; margin-top: 4px;
  }
  .save-entry-btn:active { transform: scale(.97); }

  .past-entries { margin-top: 20px; }
  .entry-card {
    background: var(--surface); border: 1px solid var(--border);
    border-radius: 14px; padding: 14px; margin-bottom: 8px;
    display: flex; gap: 12px; align-items: center;
  }
  .entry-emojis { font-size: 18px; display: flex; gap: 4px; }
  .entry-meta { flex: 1; }
  .entry-meta .et { font-size: 11px; color: var(--muted); }
  .entry-meta .ed { font-size: 13px; color: var(--text); margin-top: 2px; }

  /* ── SOS MODAL ── */
  .modal-overlay {
    position: fixed; inset: 0; z-index: 200;
    background: rgba(0,0,0,.8); backdrop-filter: blur(8px);
    display: none; align-items: flex-end; justify-content: center;
    animation: fadeIn .3s ease;
  }
  .modal-overlay.open { display: flex; }
  @keyframes fadeIn { from{opacity:0} to{opacity:1} }
  .modal-sheet {
    background: var(--surface); border-radius: 24px 24px 0 0;
    padding: 28px 24px 40px; width: 100%; max-width: 480px;
    animation: slideUp .3s ease;
  }
  @keyframes slideUp { from{transform:translateY(40px);opacity:0} to{transform:translateY(0);opacity:1} }
  .modal-handle {
    width: 36px; height: 4px; border-radius: 4px;
    background: var(--border); margin: 0 auto 24px;
  }
  .modal-sheet h2 { font-family: 'Syne', sans-serif; font-size: 22px; font-weight: 800; margin-bottom: 8px; }
  .modal-sheet p { font-size: 14px; color: var(--muted); line-height: 1.6; margin-bottom: 20px; }
  .breath-circle {
    width: 100px; height: 100px; border-radius: 50%;
    background: linear-gradient(135deg, rgba(79,195,247,.2), rgba(2,136,209,.1));
    border: 2px solid rgba(79,195,247,.4);
    margin: 0 auto 20px;
    display: flex; align-items: center; justify-content: center;
    font-size: 12px; font-weight: 600; color: var(--accent);
    animation: breathe 4s ease-in-out infinite;
  }
  @keyframes breathe { 0%,100%{transform:scale(.9);opacity:.7} 50%{transform:scale(1.1);opacity:1} }
  .fact-box {
    background: var(--surface2); border-radius: 14px; padding: 16px;
    margin-bottom: 20px; font-size: 13px; line-height: 1.6;
    border-left: 3px solid var(--accent);
  }
  .modal-btns { display: flex; gap: 10px; }
  .btn-continue { flex: 1; padding: 15px; border-radius: 14px; background: linear-gradient(135deg,#4fc3f7,#0288d1); color:#fff; font-family:'Syne',sans-serif; font-size:14px; font-weight:700; border:none; cursor:pointer; }
  .btn-break { flex: 1; padding: 15px; border-radius: 14px; background: var(--surface2); border: 1px solid var(--border); color: var(--text); font-family:'Syne',sans-serif; font-size:14px; font-weight:700; cursor:pointer; }

  /* ── MISC ── */
  .page-title {
    font-family: 'Syne', sans-serif; font-size: 26px;
    font-weight: 800; letter-spacing: -.5px;
    margin-bottom: 4px;
  }
  .page-sub { font-size: 13px; color: var(--muted); margin-bottom: 20px; }
  .divider { height: 1px; background: var(--border); margin: 20px 0; }
  .toast {
    position: fixed; bottom: 90px; left: 50%; transform: translateX(-50%);
    background: var(--surface2); border: 1px solid var(--border);
    color: var(--text); padding: 12px 20px; border-radius: 100px;
    font-size: 13px; font-weight: 500; z-index: 300;
    white-space: nowrap;
    animation: toastIn .3s ease;
    pointer-events: none;
  }
  @keyframes toastIn { from{opacity:0;transform:translateX(-50%) translateY(10px)} to{opacity:1;transform:translateX(-50%) translateY(0)} }

  /* Colors per phase */
  .c0 { color: var(--phase0); } .b0 { border-color: var(--phase0) !important; }
  .c1 { color: var(--phase1); } .b1 { border-color: var(--phase1) !important; }
  .c2 { color: var(--phase2); } .b2 { border-color: var(--phase2) !important; }
  .c3 { color: var(--phase3); } .b3 { border-color: var(--phase3) !important; }
  .c4 { color: var(--phase4); } .b4 { border-color: var(--phase4) !important; }

  .bg0 { background: rgba(124,131,253,.12); }
  .bg1 { background: rgba(249,168,37,.12); }
  .bg2 { background: rgba(255,112,67,.12); }
  .bg3 { background: rgba(102,187,106,.12); }
  .bg4 { background: rgba(38,198,218,.12); }

  @media (prefers-reduced-motion: reduce) {
    *, *::before, *::after { animation-duration: 0.01ms !important; }
  }
</style>
</head>
<body>

<!-- SPLASH -->
<div id="splash">
  <div class="pulse-ring"></div>
  <div class="logo-big">Fast<span>Flow</span></div>
  <p>Mapa Biológico em Tempo Real</p>
</div>

<!-- APP -->
<div id="app">
  <header>
    <div class="logo">Fast<span>Flow</span></div>
    <div class="header-right">
      <div class="icon-btn" id="notifBtn" title="Notificações">🔔</div>
    </div>
  </header>

  <div class="scroll-area">
    <!-- ─── TIMER SCREEN ─── -->
    <div class="screen active" id="screen-timer">

      <!-- Phase Banner -->
      <div class="phase-banner" id="phaseBanner">
        <div class="phase-icon" id="phaseIcon">🌅</div>
        <div class="phase-info">
          <h3 id="phaseName">Inicie seu jejum</h3>
          <p id="phaseDesc">Escolha um protocolo e comece sua jornada biológica.</p>
        </div>
      </div>

      <!-- Ring Timer -->
      <div class="ring-wrap">
        <div class="ring-container">
          <svg class="ring-svg" viewBox="0 0 220 220">
            <circle class="ring-track" cx="110" cy="110" r="100"/>
            <circle class="ring-fill" id="ringFill" cx="110" cy="110" r="100"/>
          </svg>
          <div class="ring-center">
            <div class="timer-display" id="timerDisplay">00:00:00</div>
            <div class="timer-label" id="timerLabel">AGUARDANDO</div>
            <div class="timer-pct" id="timerPct"></div>
          </div>
        </div>
      </div>

      <!-- Stats Row -->
      <div class="stats-row">
        <div class="stat-card">
          <div class="sv" id="statProtocol">—</div>
          <div class="sl">Protocolo</div>
        </div>
        <div class="stat-card">
          <div class="sv" id="statTarget">—</div>
          <div class="sl">Meta (h)</div>
        </div>
        <div class="stat-card">
          <div class="sv" id="statRemaining">—</div>
          <div class="sl">Restante</div>
        </div>
      </div>

      <!-- CTA -->
      <div class="cta-row" id="ctaRow">
        <button class="btn-primary btn-start" id
