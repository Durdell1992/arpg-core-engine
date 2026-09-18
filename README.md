# arpg-core-engine

An atmospheric, deep-systems isometric ARPG demo inspired by *Path of Exile*. 

[![Play Demo](https://img.shields.io/badge/Play-Live%20Demo-brightgreen?style=for-the-badge&logo=google-chrome)](https://durdell.com/sadbot.html)

---

## 🎮 Game Code Showcase

<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Wraeclast Depths - PoE & D2 ARPG Engine</title>
<style>
  * { box-sizing: border-box; margin: 0; padding: 0; user-select: none; }
  body {
    background-color: #020402;
    color: #c4beaf;
    font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
    overflow: hidden;
    height: 100vh;
    display: flex;
  }

  /* Viewport */
  #game-container {
    position: relative;
    flex: 1;
    height: 100vh;
    background: #010401;
    overflow: hidden;
  }
  canvas { display: block; width: 100%; height: 100%; }

  /* Top HUD */
  #hud {
    position: absolute;
    top: 14px;
    left: 14px;
    display: flex;
    align-items: center;
    gap: 8px;
    font-family: 'Courier New', monospace;
    font-size: 11.5px;
    z-index: 10;
  }
  .hud-card {
    background: rgba(8, 12, 8, 0.92);
    border: 1px solid #2b3d2c;
    padding: 5px 9px;
    border-radius: 4px;
    display: flex;
    align-items: center;
    gap: 5px;
    box-shadow: 0 4px 15px rgba(0,0,0,0.85);
  }
  #pause-btn {
    background: #192b1b;
    border: 1px solid #3e6342;
    color: #9fe0a4;
    padding: 5px 10px;
    font-family: inherit;
    font-weight: bold;
    cursor: pointer;
    border-radius: 3px;
    transition: 0.15s;
  }
  #pause-btn.paused { background: #695217; border-color: #ffd748; color: #fff; }

  /* Boss Bar */
  #boss-bar-container {
    position: absolute;
    top: 20px;
    left: 50%;
    transform: translateX(-50%);
    width: 460px;
    display: none;
    flex-direction: column;
    align-items: center;
    z-index: 15;
  }
  #boss-name {
    font-family: 'Cinzel', serif, Georgia;
    font-size: 14px;
    font-weight: bold;
    letter-spacing: 2px;
    color: #ffd748;
    text-shadow: 0 2px 8px #000;
    margin-bottom: 4px;
  }
  .boss-bar-bg {
    width: 100%;
    height: 16px;
    background: #1a0808;
    border: 2px solid #8f6f26;
    border-radius: 3px;
    overflow: hidden;
    box-shadow: 0 0 15px rgba(0,0,0,0.9);
  }
  #boss-bar-fill {
    width: 100%;
    height: 100%;
    background: linear-gradient(90deg, #821010, #e62020);
    transition: width 0.1s linear;
  }

  /* POE 10-SEGMENT EXP BAR */
  #exp-bar-container {
    position: absolute;
    bottom: 0;
    left: 0;
    width: 100%;
    height: 8px;
    background: #060907;
    border-top: 1px solid #2b3d2c;
    display: flex;
    z-index: 18;
  }
  .exp-segment {
    flex: 1;
    height: 100%;
    border-right: 1px solid #142115;
    background: #09120a;
    position: relative;
    overflow: hidden;
  }
  .exp-segment:last-child { border-right: none; }
  .exp-segment-fill {
    height: 100%;
    width: 0%;
    background: linear-gradient(90deg, #1b6329, #4ade80);
    transition: width 0.1s linear;
  }

  /* DIABLO 2 DUAL GLOBES & ACTION BAR */
  #d2-bar-wrapper {
    position: absolute;
    bottom: 14px;
    left: 50%;
    transform: translateX(-50%);
    display: flex;
    align-items: flex-end;
    gap: 12px;
    z-index: 15;
  }

  .globe-container {
    position: relative;
    display: flex;
    flex-direction: column;
    align-items: center;
  }
  .d2-globe {
    width: 84px;
    height: 84px;
    border-radius: 50%;
    background: #080c08;
    border: 3px solid #6b582b;
    position: relative;
    overflow: hidden;
    box-shadow: 0 0 20px rgba(0,0,0,0.95), inset 0 0 12px rgba(0,0,0,0.8);
  }
  .d2-liquid {
    position: absolute;
    bottom: 0;
    left: 0;
    width: 100%;
    transition: height 0.15s ease-out;
  }
  #life-liquid { height: 100%; background: radial-gradient(circle at 50% 30%, #ff3b3b, #780808); }
  #mana-liquid { height: 100%; background: radial-gradient(circle at 50% 30%, #38bdf8, #0c2d6b); }
  .globe-shine {
    position: absolute;
    top: 4px;
    left: 12px;
    width: 56px;
    height: 28px;
    border-radius: 50%;
    background: linear-gradient(180deg, rgba(255,255,255,0.4), transparent);
    pointer-events: none;
  }
  .globe-text {
    position: absolute;
    width: 100%;
    text-align: center;
    bottom: 30px;
    font-family: 'Courier New', monospace;
    font-size: 11px;
    font-weight: bold;
    color: #fff;
    text-shadow: 0 1px 4px #000, 0 0 8px #000;
    pointer-events: none;
  }

  /* Yellow Rage Bar */
  #rage-bar-wrapper {
    position: absolute;
    top: -12px;
    left: 50%;
    transform: translateX(-50%);
    width: 78px;
    height: 8px;
    background: #1c1505;
    border: 1px solid #7a5c17;
    border-radius: 4px;
    overflow: hidden;
    display: none;
    box-shadow: 0 0 8px rgba(255, 185, 0, 0.4);
  }
  #rage-bar-fill {
    width: 0%;
    height: 100%;
    background: linear-gradient(90deg, #d48b00, #ffea00);
    transition: width 0.1s linear;
  }

  /* Action Slots */
  #action-bar {
    display: flex;
    gap: 6px;
    background: rgba(6, 9, 6, 0.94);
    border: 2px solid #384236;
    padding: 6px 8px;
    border-radius: 6px;
    box-shadow: 0 0 25px rgba(0,0,0,0.9);
    margin-bottom: 8px;
  }
  .action-slot {
    width: 50px;
    height: 50px;
    background: #0d120d;
    border: 1px solid #4a5c48;
    border-radius: 4px;
    display: flex;
    flex-direction: column;
    align-items: center;
    justify-content: center;
    position: relative;
    color: #e0e0e0;
    font-size: 9.5px;
    text-align: center;
    padding: 2px;
  }
  .action-slot .key-label {
    position: absolute;
    top: 2px;
    left: 3px;
    font-size: 9px;
    color: #ffd748;
    font-family: monospace;
    font-weight: bold;
  }
  .action-slot.active-gem {
    border-color: #38bdf8;
    background: #11222b;
    box-shadow: inset 0 0 8px rgba(56, 189, 248, 0.45);
  }
  .action-slot.empty { opacity: 0.45; }

  /* Talent Tree Button */
  #book-btn {
    width: 60px;
    height: 72px;
    background: #1c140c;
    border: 2px solid #8f6f26;
    border-radius: 5px;
    display: flex;
    flex-direction: column;
    align-items: center;
    justify-content: center;
    cursor: pointer;
    box-shadow: 0 0 15px rgba(0,0,0,0.9);
    margin-bottom: 6px;
    position: relative;
    transition: 0.15s;
  }
  #book-btn:hover {
    background: #2b1d12;
    border-color: #ffd748;
    box-shadow: 0 0 15px rgba(255, 215, 72, 0.4);
  }
  #book-btn .book-icon { font-size: 26px; }
  #book-btn .book-text {
    font-size: 8.5px;
    font-weight: bold;
    color: #ffd748;
    letter-spacing: 1px;
    margin-top: 2px;
  }
  #tree-points-badge {
    position: absolute;
    top: -6px;
    right: -6px;
    background: #991b1b;
    border: 1px solid #ffd748;
    color: #fff;
    font-size: 10px;
    font-weight: bold;
    border-radius: 50%;
    width: 20px;
    height: 20px;
    display: flex;
    align-items: center;
    justify-content: center;
  }

  /* PoE Sidebar */
  #sidebar {
    width: 480px;
    height: 100vh;
    background: #090c0a;
    border-left: 2px solid #223023;
    display: flex;
    flex-direction: column;
    padding: 12px;
    gap: 8px;
    overflow-y: auto;
    box-shadow: -8px 0 30px rgba(0,0,0,0.95);
    z-index: 20;
  }
  .panel-header {
    font-size: 11px;
    font-weight: bold;
    letter-spacing: 2px;
    color: #a4916a;
    text-transform: uppercase;
    border-bottom: 1px solid #1f2b20;
    padding-bottom: 3px;
  }

  /* Paperdoll */
  #paperdoll-grid {
    display: grid;
    grid-template-columns: repeat(3, 1fr);
    gap: 5px;
    background: #060907;
    border: 1px solid #243325;
    padding: 6px;
    border-radius: 4px;
  }
  .equip-slot {
    height: 44px;
    background: #0b110c;
    border: 1px dashed #344735;
    border-radius: 3px;
    display: flex;
    flex-direction: column;
    align-items: center;
    justify-content: center;
    text-align: center;
    font-size: 9.5px;
    color: #617361;
    cursor: pointer;
    padding: 2px;
    transition: 0.15s;
  }
  .equip-slot:hover { border-color: #5af078; background: #131c14; }
  .equip-slot.filled { border-style: solid; border-color: #8da3c7; color: #fff; background: #101812; }
  .equip-slot.slot-chest { grid-column: 2; height: 50px; }

  /* 6-Link Sockets */
  #socket-panel {
    background: #0b100c;
    border: 1px solid #2e3e2f;
    border-radius: 4px;
    padding: 6px;
    display: flex;
    flex-direction: column;
    gap: 4px;
  }
  .sockets-container {
    display: grid;
    grid-template-columns: repeat(6, 1fr);
    gap: 5px;
    align-items: center;
    justify-items: center;
    background: #050805;
    padding: 6px 3px;
    border: 1px solid #1c281d;
    border-radius: 3px;
  }
  .socket {
    width: 36px;
    height: 36px;
    border-radius: 50%;
    background: #080c09;
    border: 2px solid #555;
    display: flex;
    align-items: center;
    justify-content: center;
    font-size: 9px;
    text-align: center;
    cursor: pointer;
    transition: 0.15s;
    font-weight: bold;
    color: #fff;
  }
  .socket:hover { transform: scale(1.08); }
  .socket.gem-active { border-color: #f87171; background: #3b1111; box-shadow: 0 0 6px #f87171; }
  .socket.gem-support { border-color: #60a5fa; background: #11233b; box-shadow: 0 0 6px #60a5fa; }
  .socket-link-line {
    font-size: 9px;
    color: #d4af37;
    font-weight: bold;
    text-align: center;
  }

  /* Crafting Altar */
  #crafting-bench {
    background: #0d120e;
    border: 1px solid #2e3e2f;
    border-radius: 4px;
    padding: 6px 10px;
    display: flex;
    flex-direction: column;
    gap: 6px;
  }
  .bench-row { display: flex; justify-content: space-around; align-items: center; }
  .bench-slot {
    width: 140px;
    height: 40px;
    background: #060907;
    border: 1px dashed #3a4a3a;
    border-radius: 3px;
    display: flex;
    align-items: center;
    justify-content: center;
    text-align: center;
    font-size: 10px;
    color: #617361;
    padding: 3px;
  }
  .bench-slot.loaded { border-style: solid; color: #fff; }
  #slam-btn {
    background: #471a1a;
    border: 1px solid #8f3232;
    color: #f7a8a8;
    padding: 6px;
    font-weight: bold;
    cursor: pointer;
    text-transform: uppercase;
    border-radius: 3px;
    font-size: 10.5px;
  }
  #slam-btn:hover:not(:disabled) { background: #6b2222; color: #fff; }
  #slam-btn:disabled { opacity: 0.35; cursor: not-allowed; }

  /* Stash */
  #stash-container {
    display: flex;
    flex-direction: column;
    background: #070a08;
    border: 1px solid #263627;
    border-radius: 4px;
    padding: 6px;
    gap: 6px;
  }
  .stash-tabs { display: flex; gap: 4px; }
  .stash-tab-btn {
    flex: 1;
    background: #111812;
    border: 1px solid #2b3d2c;
    color: #8da38f;
    padding: 5px 2px;
    font-size: 10.5px;
    font-weight: bold;
    cursor: pointer;
    border-radius: 3px;
    text-align: center;
    transition: 0.15s;
  }
  .stash-tab-btn.active {
    background: #243525;
    border-color: #a4916a;
    color: #ffd748;
  }

  .stash-grid {
    display: grid;
    grid-template-columns: repeat(8, 1fr);
    grid-template-rows: repeat(5, 46px);
    gap: 4px;
    background: #030503;
    border: 1px solid #1a251b;
    padding: 4px;
    border-radius: 3px;
  }
  .stash-cell {
    width: 100%;
    height: 100%;
    background: #0a0e0b;
    border: 1px solid #1d2b1f;
    border-radius: 2px;
    display: flex;
    align-items: center;
    justify-content: center;
    text-align: center;
    font-size: 9px;
    cursor: pointer;
    position: relative;
    padding: 2px;
    transition: 0.1s;
    color: #7d917d;
  }
  .stash-cell:hover { border-color: #5af078; background: #131c14; }
  .stash-cell.occupied { border-color: #3b523c; color: #fff; font-weight: bold; }
  .stash-cell.selected { border-color: #ffd748 !important; background: #212c1d !important; }

  .item-gray { color: #9e9e9e !important; }
  .item-rare { color: #ffd748 !important; }
  .item-organ { color: #ff5454 !important; }
  .item-heart { color: #f43f5e !important; font-weight: bold; }
  .item-gem-active { color: #f87171 !important; }
  .item-gem-support { color: #60a5fa !important; }

  /* Universal Tooltip */
  #poe-tooltip {
    position: fixed;
    display: none;
    background: rgba(8, 11, 8, 0.96);
    border: 1px solid #a4916a;
    box-shadow: 0 4px 20px rgba(0,0,0,0.95);
    padding: 8px 12px;
    border-radius: 3px;
    pointer-events: none;
    z-index: 100;
    max-width: 280px;
    font-size: 11px;
    line-height: 1.35;
  }
  #poe-tooltip .tt-name { font-size: 12px; font-weight: bold; margin-bottom: 2px; }
  #poe-tooltip .tt-sub { font-size: 10px; color: #7f917f; margin-bottom: 6px; border-bottom: 1px solid #283629; padding-bottom: 3px; }
  #poe-tooltip .tt-affix { color: #8da3c7; font-size: 10.5px; }

  /* 800-NODE PASSIVE TREE MODAL */
  #talent-modal {
    position: fixed;
    top: 0;
    left: 0;
    width: 100vw;
    height: 100vh;
    background: rgba(2, 5, 2, 0.98);
    display: none;
    flex-direction: column;
    z-index: 60;
  }
  #talent-header {
    background: #090e0a;
    border-bottom: 2px solid #3d4f3b;
    padding: 10px 20px;
    display: flex;
    justify-content: space-between;
    align-items: center;
    box-shadow: 0 4px 15px rgba(0,0,0,0.8);
  }
  .talent-title {
    font-family: 'Cinzel', serif, Georgia;
    font-size: 15px;
    font-weight: bold;
    color: #ffd748;
    letter-spacing: 2px;
  }
  .talent-stats-summary {
    display: flex;
    gap: 16px;
    font-size: 12px;
    font-family: monospace;
  }
  .talent-stats-summary span { color: #5af078; font-weight: bold; }
  #close-tree-btn {
    background: #471a1a;
    border: 1px solid #8f3232;
    color: #f7a8a8;
    padding: 6px 16px;
    font-weight: bold;
    cursor: pointer;
    border-radius: 3px;
    letter-spacing: 1px;
  }
  #close-tree-btn:hover { background: #6e2424; color: #fff; }
  #tree-canvas-container {
    flex: 1;
    position: relative;
    overflow: hidden;
    cursor: grab;
  }
  #tree-canvas-container:active { cursor: grabbing; }
  #tree-canvas { width: 100%; height: 100%; display: block; }
  #tree-hint {
    position: absolute;
    bottom: 15px;
    left: 20px;
    background: rgba(6, 10, 6, 0.85);
    border: 1px solid #233523;
    padding: 6px 14px;
    font-size: 11px;
    color: #a4916a;
    font-family: monospace;
    border-radius: 3px;
    pointer-events: none;
  }

  /* Tree Hover Tooltip */
  #tree-tooltip {
    position: absolute;
    display: none;
    background: rgba(10, 14, 10, 0.97);
    border: 1.5px solid #d4af37;
    padding: 9px 13px;
    border-radius: 4px;
    pointer-events: none;
    z-index: 70;
    max-width: 250px;
    box-shadow: 0 4px 20px rgba(0,0,0,0.9);
    font-size: 11px;
  }
  #tree-tooltip .tt-tname { font-size: 12.5px; font-weight: bold; color: #ffd748; margin-bottom: 3px; }
  #tree-tooltip .tt-tcost { font-size: 10px; color: #5af078; font-family: monospace; margin-bottom: 5px; border-bottom: 1px solid #283a2a; padding-bottom: 3px; }
  #tree-tooltip .tt-tdesc { color: #b8c7b8; font-size: 11px; line-height: 1.35; }

  /* Death Screen */
  #death-screen {
    position: absolute;
    top: 0;
    left: 0;
    width: 100%;
    height: 100%;
    background: rgba(40, 0, 0, 0.85);
    display: none;
    flex-direction: column;
    align-items: center;
    justify-content: center;
    z-index: 50;
    backdrop-filter: blur(4px);
  }
  #death-screen h1 {
    font-family: 'Cinzel', serif, Georgia;
    font-size: 46px;
    color: #e62020;
    letter-spacing: 4px;
    text-shadow: 0 0 20px rgba(255, 0, 0, 0.8);
    margin-bottom: 8px;
  }
  #death-screen p {
    font-size: 14px;
    color: #baa38f;
    font-style: italic;
    margin-bottom: 24px;
  }
  #resurrect-btn {
    background: #381212;
    border: 2px solid #a83232;
    color: #f7a8a8;
    padding: 10px 24px;
    font-size: 13px;
    font-weight: bold;
    letter-spacing: 2px;
    cursor: pointer;
    border-radius: 4px;
    transition: 0.2s;
  }
  #resurrect-btn:hover { background: #5e1c1c; color: #fff; box-shadow: 0 0 15px #ff4d4d; }

  #pause-overlay {
    position: absolute;
    top: 50%;
    left: 50%;
    transform: translate(-50%, -50%);
    background: rgba(8, 12, 8, 0.94);
    border: 2px solid #a4916a;
    padding: 16px 36px;
    text-align: center;
    border-radius: 6px;
    display: none;
    z-index: 25;
    pointer-events: none;
  }
</style>
</head>
<body>

<div id="game-container">
  <canvas id="stage"></canvas>

  <div id="hud">
    <div class="hud-card">🌊 WAVE: <span style="color:#38bdf8; font-weight:bold;" id="wave-val">1 / 100</span></div>
    <div class="hud-card">⏳ SURVIVE: <span style="color:#fde047; font-weight:bold;" id="wave-timer-val">01:00</span></div>
    <div class="hud-card">LVL: <span style="color:#ffd748; font-weight:bold;" id="lvl-val">1</span></div>
    <div class="hud-card">EXP: <span style="color:#5af078; font-weight:bold;" id="exp-val">0/100</span></div>
    <div class="hud-card">🛡️ ARMOUR: <span style="color:#8da3c7; font-weight:bold;" id="armour-val">0</span></div>
    <div class="hud-card">🔥 <span style="color:#f87171; font-weight:bold;" id="fres-val">0%</span></div>
    <div class="hud-card">❄️ <span style="color:#60a5fa; font-weight:bold;" id="cres-val">0%</span></div>
    <div class="hud-card">⚡ <span style="color:#fde047; font-weight:bold;" id="lres-val">0%</span></div>
    <button id="pause-btn" onclick="togglePause()">[P] PAUSE / CRAFT</button>
  </div>

  <div id="boss-bar-container">
    <div id="boss-name">MALOK, THE SKELETAL NECROMANCER</div>
    <div class="boss-bar-bg"><div id="boss-bar-fill"></div></div>
  </div>

  <div id="d2-bar-wrapper">
    <div class="globe-container">
      <div id="rage-bar-wrapper"><div id="rage-bar-fill"></div></div>
      <div class="d2-globe">
        <div class="d2-liquid" id="life-liquid"></div>
        <div class="globe-shine"></div>
        <div class="globe-text" id="life-text">1000</div>
      </div>
    </div>

    <div id="action-bar">
      <div class="action-slot" id="bar-slot-0"><span class="key-label">LMB</span><span>Default Strike</span></div>
      <div class="action-slot empty" id="bar-slot-1"><span class="key-label">RMB</span><span class="slot-name">Empty</span></div>
      <div class="action-slot empty" id="bar-slot-2"><span class="key-label">Q</span><span class="slot-name">Empty</span></div>
      <div class="action-slot empty" id="bar-slot-3"><span class="key-label">W</span><span class="slot-name">Empty</span></div>
      <div class="action-slot empty" id="bar-slot-4"><span class="key-label">E</span><span class="slot-name">Empty</span></div>
      <div class="action-slot empty" id="bar-slot-5"><span class="key-label">R</span><span class="slot-name">Empty</span></div>
      <div class="action-slot empty" id="bar-slot-6"><span class="key-label">T</span><span class="slot-name">Empty</span></div>
    </div>

    <div class="globe-container">
      <div class="d2-globe">
        <div class="d2-liquid" id="mana-liquid"></div>
        <div class="globe-shine"></div>
        <div class="globe-text" id="mana-text">250</div>
      </div>
    </div>

    <div id="book-btn" onclick="toggleTalentTree()" title="Open Passive Skill Tree (800 Nodes)">
      <div id="tree-points-badge">15</div>
      <div class="book-icon">📖</div>
      <div class="book-text">TALENTS</div>
    </div>
  </div>

  <div id="exp-bar-container">
    <div class="exp-segment"><div class="exp-segment-fill" id="exp-seg-0"></div></div>
    <div class="exp-segment"><div class="exp-segment-fill" id="exp-seg-1"></div></div>
    <div class="exp-segment"><div class="exp-segment-fill" id="exp-seg-2"></div></div>
    <div class="exp-segment"><div class="exp-segment-fill" id="exp-seg-3"></div></div>
    <div class="exp-segment"><div class="exp-segment-fill" id="exp-seg-4"></div></div>
    <div class="exp-segment"><div class="exp-segment-fill" id="exp-seg-5"></div></div>
    <div class="exp-segment"><div class="exp-segment-fill" id="exp-seg-6"></div></div>
    <div class="exp-segment"><div class="exp-segment-fill" id="exp-seg-7"></div></div>
    <div class="exp-segment"><div class="exp-segment-fill" id="exp-seg-8"></div></div>
    <div class="exp-segment"><div class="exp-segment-fill" id="exp-seg-9"></div></div>
  </div>

  <div id="death-screen">
    <h1>YOU HAVE DIED</h1>
    <p>Your deeds of valor will be remembered.</p>
    <button id="resurrect-btn" onclick="resurrectHero()">RESURRECT IN CAMP</button>
  </div>

  <div id="pause-overlay">
    <h2 style="color:#ffd748; letter-spacing:2px;">GAME PAUSED</h2>
    <p style="font-size:12px; color:#8ea38c; margin-top:4px;">Enemies frozen. Safe to socket gems and reforge gear.</p>
  </div>
</div>

<!-- PoE Sidebar -->
<div id="sidebar">
  <div class="panel-header">Character Equipment</div>
  <div id="paperdoll-grid">
    <div class="equip-slot" id="slot-amulet" onclick="unequipSlot('amulet')">Amulet</div>
    <div class="equip-slot" id="slot-helmet" onclick="unequipSlot('helmet')">Helmet</div>
    <div></div>

    <div class="equip-slot" id="slot-mainhand" onclick="unequipSlot('mainhand')">Main Hand</div>
    <div class="equip-slot slot-chest" id="slot-chest" onclick="unequipSlot('chest')">Body Armour (6S)</div>
    <div class="equip-slot" id="slot-offhand" onclick="unequipSlot('offhand')">Off Hand</div>

    <div class="equip-slot" id="slot-ring1" onclick="unequipSlot('ring1')">Ring 1</div>
    <div class="equip-slot" id="slot-belt" onclick="unequipSlot('belt')">Belt</div>
    <div class="equip-slot" id="slot-ring2" onclick="unequipSlot('ring2')">Ring 2</div>
  </div>

  <div id="socket-panel">
    <div style="display:flex; justify-content:space-between; align-items:center;">
      <span class="panel-header" style="border:none; padding:0;">Body Armour (6-Linked Sockets)</span>
      <span style="font-size:9px; color:#ffd748;">Click socket to unsocket</span>
    </div>
    <div class="sockets-container" id="sockets-container"></div>
    <div class="socket-link-line">&mdash;&mdash; LINKED &mdash;&mdash;</div>
  </div>

  <div class="panel-header">Flesh Crafting Altar</div>
  <div id="crafting-bench">
    <div class="bench-row">
      <div class="bench-slot" id="bench-item-slot">Click Base Item</div>
      <div style="color:#666; font-weight:bold;">+</div>
      <div class="bench-slot" id="bench-organ-slot">Click Liver / Heart</div>
    </div>
    <button id="slam-btn" disabled onclick="executeCraft()">Apply Organ</button>
  </div>

  <div id="stash-container">
    <div class="stash-tabs">
      <button class="stash-tab-btn active" id="tab-btn-gear" onclick="switchStashTab('gear')">Items Stash (<span id="count-gear">0</span>)</button>
      <button class="stash-tab-btn" id="tab-btn-organs" onclick="switchStashTab('organs')">Organs (<span id="count-organs">0</span>)</button>
      <button class="stash-tab-btn" id="tab-btn-gems" onclick="switchStashTab('gems')">Gems (<span id="count-gems">0</span>)</button>
    </div>
    <div class="stash-grid" id="stash-grid"></div>
    <div style="font-size:9.5px; color:#6d806d; text-align:center;">
      Right-Click to Equip &bull; Left-Click to Select for Altar
    </div>
  </div>
</div>

<!-- 800-NODE PASSIVE TREE MODAL -->
<div id="talent-modal">
  <div id="talent-header">
    <div class="talent-title">PATH OF EXILE PASSIVE CONSTELLATION (800 NODES)</div>
    <div class="talent-stats-summary">
      <div>POINTS: <span id="summary-points">15</span></div>
      <div>STR: <span id="summary-str">0</span></div>
      <div>INT: <span id="summary-int">0</span></div>
      <div>DEX: <span id="summary-dex">0</span></div>
      <div>RAGE: <span id="summary-rage">0/0</span></div>
    </div>
    <button id="close-tree-btn" onclick="toggleTalentTree()">[X] CLOSE CONSTELLATION</button>
  </div>
  <div id="tree-canvas-container">
    <canvas id="tree-canvas"></canvas>
    <div id="tree-hint">
      [DRAG] Pan Tree &bull; [SCROLL] Zoom &bull; Small Node: 1 Point &bull; Big Keystone / Notable: 5 Points &bull; [U / BOOK] Toggle
    </div>
    <div id="tree-tooltip">
      <div class="tt-tname" id="tt-tname">Node Name</div>
      <div class="tt-tcost" id="tt-tcost">Cost: 1 Point</div>
      <div class="tt-tdesc" id="tt-tdesc">Node attribute details...</div>
    </div>
  </div>
</div>

<div id="poe-tooltip">
  <div class="tt-name" id="tt-name">Item Name</div>
  <div class="tt-sub" id="tt-sub">Item Type</div>
  <div id="tt-affixes"></div>
</div>

<script>
// --- AUDIO SYNTHESIS ENGINE ---
const audioCtx = new (window.AudioContext || window.webkitAudioContext)();

function playSound(type) {
  if (audioCtx.state === 'suspended') audioCtx.resume();
  const t = audioCtx.currentTime;
  const osc = audioCtx.createOscillator();
  const gain = audioCtx.createGain();

  if (type === 'slash') {
    osc.type = 'sawtooth';
    osc.frequency.setValueAtTime(320, t);
    osc.frequency.exponentialRampToValueAtTime(45, t + 0.1);
    gain.gain.setValueAtTime(0.18, t);
    gain.gain.exponentialRampToValueAtTime(0.01, t + 0.1);
    osc.connect(gain); gain.connect(audioCtx.destination);
    osc.start(); osc.stop(t + 0.1);
  } else if (type === 'bow') {
    osc.type = 'triangle';
    osc.frequency.setValueAtTime(420, t);
    osc.frequency.exponentialRampToValueAtTime(90, t + 0.14);
    gain.gain.setValueAtTime(0.2, t);
    gain.gain.exponentialRampToValueAtTime(0.01, t + 0.14);
    osc.connect(gain); gain.connect(audioCtx.destination);
    osc.start(); osc.stop(t + 0.14);
  } else if (type === 'clack') {
    osc.type = 'square';
    osc.frequency.setValueAtTime(580, t);
    osc.frequency.exponentialRampToValueAtTime(140, t + 0.08);
    gain.gain.setValueAtTime(0.12, t);
    gain.gain.exponentialRampToValueAtTime(0.01, t + 0.08);
    osc.connect(gain); gain.connect(audioCtx.destination);
    osc.start(); osc.stop(t + 0.08);
  } else if (type === 'flicker') {
    osc.type = 'sine';
    osc.frequency.setValueAtTime(800, t);
    osc.frequency.exponentialRampToValueAtTime(140, t + 0.15);
    gain.gain.setValueAtTime(0.3, t);
    gain.gain.exponentialRampToValueAtTime(0.01, t + 0.15);
    osc.connect(gain); gain.connect(audioCtx.destination);
    osc.start(); osc.stop(t + 0.15);
  } else if (type === 'molten') {
    osc.type = 'square';
    osc.frequency.setValueAtTime(160, t);
    osc.frequency.exponentialRampToValueAtTime(25, t + 0.28);
    gain.gain.setValueAtTime(0.3, t);
    gain.gain.exponentialRampToValueAtTime(0.01, t + 0.3);
    osc.connect(gain); gain.connect(audioCtx.destination);
    osc.start(); osc.stop(t + 0.3);
  } else if (type === 'toxic') {
    osc.type = 'triangle';
    osc.frequency.setValueAtTime(520, t);
    osc.frequency.exponentialRampToValueAtTime(170, t + 0.18);
    gain.gain.setValueAtTime(0.2, t);
    gain.gain.exponentialRampToValueAtTime(0.01, t + 0.18);
    osc.connect(gain); gain.connect(audioCtx.destination);
    osc.start(); osc.stop(t + 0.18);
  } else if (type === 'summon') {
    osc.type = 'sawtooth';
    osc.frequency.setValueAtTime(90, t);
    osc.frequency.exponentialRampToValueAtTime(260, t + 0.3);
    gain.gain.setValueAtTime(0.25, t);
    gain.gain.exponentialRampToValueAtTime(0.01, t + 0.3);
    osc.connect(gain); gain.connect(audioCtx.destination);
    osc.start(); osc.stop(t + 0.3);
  } else if (type === 'deflect') {
    osc.type = 'sine';
    osc.frequency.setValueAtTime(900, t);
    osc.frequency.exponentialRampToValueAtTime(250, t + 0.12);
    gain.gain.setValueAtTime(0.25, t);
    gain.gain.exponentialRampToValueAtTime(0.01, t + 0.12);
    osc.connect(gain); gain.connect(audioCtx.destination);
    osc.start(); osc.stop(t + 0.12);
  } else if (type === 'death') {
    osc.type = 'sawtooth';
    osc.frequency.setValueAtTime(120, t);
    osc.frequency.exponentialRampToValueAtTime(25, t + 0.8);
    gain.gain.setValueAtTime(0.35, t);
    gain.gain.exponentialRampToValueAtTime(0.01, t + 0.8);
    osc.connect(gain); gain.connect(audioCtx.destination);
    osc.start(); osc.stop(t + 0.8);
  } else if (type === 'talent') {
    osc.type = 'sine';
    osc.frequency.setValueAtTime(440, t);
    osc.frequency.exponentialRampToValueAtTime(880, t + 0.2);
    gain.gain.setValueAtTime(0.25, t);
    gain.gain.exponentialRampToValueAtTime(0.01, t + 0.2);
    osc.connect(gain); gain.connect(audioCtx.destination);
    osc.start(); osc.stop(t + 0.2);
  } else if (type === 'levelup') {
    osc.type = 'triangle';
    osc.frequency.setValueAtTime(220, t);
    osc.frequency.exponentialRampToValueAtTime(880, t + 0.4);
    gain.gain.setValueAtTime(0.35, t);
    gain.gain.exponentialRampToValueAtTime(0.01, t + 0.45);
    osc.connect(gain); gain.connect(audioCtx.destination);
    osc.start(); osc.stop(t + 0.45);
  } else if (type === 'reforge') {
    osc.type = 'sawtooth';
    osc.frequency.setValueAtTime(200, t);
    osc.frequency.exponentialRampToValueAtTime(750, t + 0.35);
    gain.gain.setValueAtTime(0.3, t);
    gain.gain.exponentialRampToValueAtTime(0.01, t + 0.35);
    osc.connect(gain); gain.connect(audioCtx.destination);
    osc.start(); osc.stop(t + 0.35);
  }
}

// --- POE DATA DEFINITIONS & WEAPONS ---
const POE_BASES = {
  helmet: [
    { name: "Iron Circlet", baseArmour: 45 },
    { name: "Bone Helmet", baseArmour: 65 },
    { name: "Great Crown", baseArmour: 95 }
  ],
  amulet: [
    { name: "Coral Amulet", baseArmour: 0 },
    { name: "Amber Amulet", baseArmour: 0 },
    { name: "Jade Amulet", baseArmour: 0 }
  ],
  chest: [
    { name: "Plate Vest", baseArmour: 120 },
    { name: "Coronal Leather", baseArmour: 160 },
    { name: "Glorious Plate", baseArmour: 240 }
  ],
  mainhand: [
    { name: "Marrow Carver", baseArmour: 0 },
    { name: "Grave-Iron Greatsword", baseArmour: 0 },
    { name: "Obsidian Cleaver", baseArmour: 0 },
    { name: "Grove Bow", baseArmour: 0 },
    { name: "Thicket Bow", baseArmour: 0 },
    { name: "Imperial Bow", baseArmour: 0 },
    { name: "Iron Staff", baseArmour: 0 },
    { name: "Judgement Staff", baseArmour: 0 },
    { name: "Highborn Staff", baseArmour: 0 }
  ],
  offhand: [
    { name: "Spiked Shield", baseArmour: 75 },
    { name: "Painted Tower Shield", baseArmour: 110 },
    { name: "Bone Tower Shield", baseArmour: 145 }
  ],
  belt: [
    { name: "Leather Belt", baseArmour: 15 },
    { name: "Heavy Belt", baseArmour: 25 },
    { name: "Chain Belt", baseArmour: 35 }
  ],
  ring: [
    { name: "Iron Ring", baseArmour: 0 },
    { name: "Coral Ring", baseArmour: 0 },
    { name: "Gold Ring", baseArmour: 0 }
  ]
};

const ACTIVE_SKILL_GEMS = [
  { id: "gem_flicker", name: "Flicker Strike", type: "active", mana: 22, requires: "melee", desc: "Teleports to target with high burst damage. (Requires Melee Weapon)" },
  { id: "gem_molten", name: "Molten Strike", type: "active", mana: 16, requires: "melee", desc: "Slams ground. Fires 6 bouncing lava balls (35 with GMP). Deals armor-bypassing fire damage and stacks burn DoT (+10 DPS/hit). Faster Projectiles accelerates bounces! (Requires Melee Weapon)" },
  { id: "gem_double", name: "Double Strike", type: "active", mana: 12, requires: "melee", desc: "Performs two rapid strikes in succession. (Requires Melee Weapon)" },
  { id: "gem_zombies", name: "Summon Zombies", type: "active", mana: 35, requires: "none", desc: "Raises permanent friendly zombie minions. (35 Mana)" },
  { id: "gem_toxic", name: "Toxic Rain", type: "active", mana: 20, requires: "bow", desc: "Shoots skyward, raining down 35 arrows (70 with GMP). Zero hit damage; pure poison DoT (+25 DPS/hit, scaled by DoT Multiplier!). (Requires a Bow)" }
];

const SUPPORT_GEMS = [
  { id: "sup_1", name: "Melee Physical Damage", type: "support", desc: "More physical damage with melee hits." },
  { id: "sup_2", name: "Multistrike", type: "support", desc: "Attacks repeat rapidly." },
  { id: "sup_3", name: "Faster Attacks", type: "support", desc: "Increases attack animation speed." },
  { id: "sup_4", name: "Added Fire Damage", type: "support", desc: "Gain physical damage as extra fire." },
  { id: "sup_5", name: "Greater Multiple Projectiles", type: "support", desc: "+4 Projectiles to Bow attacks; Doubles Toxic Rain arrows from 35 to 70; Increases Molten Strike balls from 6 to 35!" },
  { id: "sup_6", name: "Faster Projectiles", type: "support", desc: "Projectiles travel much quicker; Accelerates Molten Strike lava ball travel and bounce speed!" },
  { id: "sup_7", name: "Void Manipulation", type: "support", desc: "More Chaos damage for Toxic Rain." },
  { id: "sup_8", name: "Minion Damage", type: "support", desc: "Zombies deal 75% more damage." },
  { id: "sup_9", name: "Meat Shield", type: "support", desc: "Zombies gain +100% Life and taunt." },
  { id: "sup_10", name: "Concentrated Effect", type: "support", desc: "Less AoE radius, 50% more area damage." },
  { id: "sup_11", name: "Increased Area of Effect", type: "support", desc: "50% larger skill impact area." },
  { id: "sup_12", name: "Brutality", type: "support", desc: "Massive physical hit amplifier." },
  { id: "sup_13", name: "Combustion", type: "support", desc: "Ignites enemies on fire with extra burn." },
  { id: "sup_14", name: "Withering Touch", type: "support", desc: "Toxic Rain inflicts stacks of Wither." },
  { id: "sup_15", name: "Lifetap", type: "support", desc: "Skills cost life, granting damage surge." }
];

// --- 5-TIER AFFIX TEMPLATES (3 PREFIXES, 3 SUFFIXES) ---
const PREFIX_TEMPLATES = [
  { key: "flat_phys", name: "Physical Damage", tiers: [ { t: 5, v: 8 }, { t: 4, v: 16 }, { t: 3, v: 28 }, { t: 2, v: 45 }, { t: 1, v: 70 } ], fmt: v => `Adds ${Math.round(v*0.7)} to ${v} Physical Damage` },
  { key: "flat_fire", name: "Fire Damage", tiers: [ { t: 5, v: 10 }, { t: 4, v: 20 }, { t: 3, v: 34 }, { t: 2, v: 52 }, { t: 1, v: 80 } ], fmt: v => `Adds ${Math.round(v*0.6)} to ${v} Fire Damage` },
  { key: "flat_cold", name: "Cold Damage", tiers: [ { t: 5, v: 9 }, { t: 4, v: 18 }, { t: 3, v: 32 }, { t: 2, v: 48 }, { t: 1, v: 75 } ], fmt: v => `Adds ${Math.round(v*0.6)} to ${v} Cold Damage` },
  { key: "flat_lightning", name: "Lightning Damage", tiers: [ { t: 5, v: 14 }, { t: 4, v: 28 }, { t: 3, v: 48 }, { t: 2, v: 76 }, { t: 1, v: 110 } ], fmt: v => `Adds 2 to ${v} Lightning Damage` },
  { key: "crit_mult", name: "Global Critical Multiplier", tiers: [ { t: 5, v: 0.10 }, { t: 4, v: 0.18 }, { t: 3, v: 0.28 }, { t: 2, v: 0.38 }, { t: 1, v: 0.50 } ], fmt: v => `+${Math.round(v*100)}% Global Critical Strike Multiplier` },
  { key: "life", name: "Maximum Life", tiers: [ { t: 5, v: 25 }, { t: 4, v: 45 }, { t: 3, v: 70 }, { t: 2, v: 105 }, { t: 1, v: 145 } ], fmt: v => `+${v} to Maximum Life` },
  { key: "armour", name: "Armour Rating", tiers: [ { t: 5, v: 35 }, { t: 4, v: 70 }, { t: 3, v: 120 }, { t: 2, v: 180 }, { t: 1, v: 260 } ], fmt: v => `+${v} to Armour` },
  { key: "dot_mult", name: "Damage Over Time Multiplier", tiers: [ { t: 5, v: 0.15 }, { t: 4, v: 0.30 }, { t: 3, v: 0.45 }, { t: 2, v: 0.60 }, { t: 1, v: 0.80 } ], fmt: v => `+${Math.round(v*100)}% to Damage Over Time Multiplier` }
];

const SUFFIX_TEMPLATES = [
  { key: "fire_res", name: "Fire Resistance", tiers: [ { t: 5, v: 0.08 }, { t: 4, v: 0.14 }, { t: 3, v: 0.20 }, { t: 2, v: 0.26 }, { t: 1, v: 0.32 } ], fmt: v => `+${Math.round(v*100)}% to Fire Resistance` },
  { key: "cold_res", name: "Cold Resistance", tiers: [ { t: 5, v: 0.08 }, { t: 4, v: 0.14 }, { t: 3, v: 0.20 }, { t: 2, v: 0.26 }, { t: 1, v: 0.32 } ], fmt: v => `+${Math.round(v*100)}% to Cold Resistance` },
  { key: "lightning_res", name: "Lightning Resistance", tiers: [ { t: 5, v: 0.08 }, { t: 4, v: 0.14 }, { t: 3, v: 0.20 }, { t: 2, v: 0.26 }, { t: 1, v: 0.32 } ], fmt: v => `+${Math.round(v*100)}% to Lightning Resistance` },
  { key: "crit_chance", name: "Critical Strike Chance", tiers: [ { t: 5, v: 0.08 }, { t: 4, v: 0.18 }, { t: 3, v: 0.30 }, { t: 2, v: 0.42 }, { t: 1, v: 0.55 } ], fmt: v => `+${Math.round(v*100)}% to Critical Strike Chance` },
  { key: "atk_spd", name: "Attack Speed", tiers: [ { t: 5, v: 0.05 }, { t: 4, v: 0.09 }, { t: 3, v: 0.13 }, { t: 2, v: 0.17 }, { t: 1, v: 0.22 } ], fmt: v => `+${Math.round(v*100)}% Increased Attack Speed` },
  { key: "str", name: "Strength", tiers: [ { t: 5, v: 12 }, { t: 4, v: 22 }, { t: 3, v: 34 }, { t: 2, v: 46 }, { t: 1, v: 58 } ], fmt: v => `+${v} to Strength` },
  { key: "dex", name: "Dexterity", tiers: [ { t: 5, v: 12 }, { t: 4, v: 22 }, { t: 3, v: 34 }, { t: 2, v: 46 }, { t: 1, v: 58 } ], fmt: v => `+${v} to Dexterity` },
  { key: "int", name: "Intelligence", tiers: [ { t: 5, v: 12 }, { t: 4, v: 22 }, { t: 3, v: 34 }, { t: 2, v: 46 }, { t: 1, v: 58 } ], fmt: v => `+${v} to Intelligence` }
];

function rollTierIndex() {
  const r = Math.random() * 100;
  if (r < 1.5) return 4; // T1 (~1.5% chance)
  if (r < 8.0) return 3; // T2
  if (r < 25.0) return 2; // T3
  if (r < 58.0) return 1; // T4
  return 0; // T5
}

function generateAffix(template) {
  const tIdx = rollTierIndex();
  const tierObj = template.tiers[tIdx];
  return {
    key: template.key,
    name: template.name,
    tier: tierObj.t,
    val: tierObj.v,
    text: `(T${tierObj.t}) ${template.fmt(tierObj.v)}`
  };
}

// --- 1-MINUTE SURVIVAL TIMER & 100 WAVES SYSTEM ---
let currentWave = 1;
const MAX_WAVES = 100;
const WAVE_DURATION_SECONDS = 60; // Exact 1-Minute Wave Duration
let waveSecondsLeft = WAVE_DURATION_SECONDS;
let waveTimerInterval = null;

// --- WORLD MAP, CAMERA ZOOM & KINEMATICS ---
const WORLD_WIDTH = 4500;
const WORLD_HEIGHT = 4500;
const CAMERA_ZOOM = 1.45;

const canvas = document.getElementById('stage');
const ctx = canvas.getContext('2d');
let isPaused = false;
let width, height;

function resize() {
  width = canvas.width = document.getElementById('game-container').clientWidth;
  height = canvas.height = document.getElementById('game-container').clientHeight;
}
window.addEventListener('resize', resize);
resize();

// HERO STATE & STATS
const hero = {
  x: 500,
  y: 500,
  radius: 17,
  speed: 1.25,
  hp: 1000,
  maxHp: 1000,
  mana: 250,
  maxMana: 250,
  manaRegen: 0.25,
  kills: 0,
  swingCooldown: 0,
  slamTimer: 0,
  shootingSkyTimer: 0,
  walkTimer: 0,
  idleTimer: 0,
  swingAngle: 0,
  isDead: false,

  level: 1,
  exp: 0,
  expToNext: 100,

  talentPoints: 15,
  strength: 0,
  intelligence: 0,
  dexterity: 0,

  // 60% Max Cap Resistances
  armour: 0,
  fireRes: 0.0,
  coldRes: 0.0,
  lightningRes: 0.0,
  dotMultiplier: 0.0,

  hasRage: false,
  rage: 0,
  maxRage: 0,
  rageDecayTimer: 0,

  ironGrip: false,
  arcaneMight: false,
  wrath: false,
  deadlyMastery: false,
  abomination: false,

  physMult: 1.0,
  flatPhys: 0,
  flatFire: 0,
  flatCold: 0,
  flatLightning: 0,
  critChance: 0.05,
  critMult: 1.5,
  attackSpeedMult: 1.0,
  bonusFlatPhys: 0
};

function getEquippedWeaponType() {
  const w = equipped.mainhand;
  if (!w) return "melee";
  const n = w.name.toLowerCase();
  if (n.includes("bow")) return "bow";
  if (n.includes("staff")) return "staff";
  return "melee";
}

// 40-Slot Stash Arrays (8x5)
const STASH_SIZE = 40;
const stashGear = new Array(STASH_SIZE).fill(null);
const stashOrgans = new Array(STASH_SIZE).fill(null);
const stashGems = new Array(STASH_SIZE).fill(null);
let activeStashTab = 'gear';
const chestSockets = [null, null, null, null, null, null];

const equipped = {
  helmet: null,
  amulet: null,
  chest: { id: 1, base: "Glorious Plate", name: "Glorious Plate", slot: "chest", rarity: "gray", baseArmour: 240, prefixes: [], suffixes: [] },
  mainhand: { id: 2, base: "Thicket Bow", name: "Thicket Bow", slot: "mainhand", rarity: "gray", baseArmour: 0, prefixes: [], suffixes: [] },
  offhand: null,
  belt: null,
  ring1: null,
  ring2: null
};

stashGear[0] = { id: Math.random(), base: "Grave-Iron Greatsword", name: "Grave-Iron Greatsword", slot: "mainhand", rarity: "gray", baseArmour: 0, prefixes: [], suffixes: [] };
stashGear[1] = { id: Math.random(), base: "Painted Tower Shield", name: "Painted Tower Shield", slot: "offhand", rarity: "gray", baseArmour: 110, prefixes: [], suffixes: [] };
stashGear[2] = { id: Math.random(), base: "Judgement Staff", name: "Judgement Staff", slot: "mainhand", rarity: "gray", baseArmour: 0, prefixes: [], suffixes: [] };
stashGear[3] = { id: Math.random(), base: "Iron Circlet", name: "Iron Circlet", slot: "helmet", rarity: "gray", baseArmour: 45, prefixes: [], suffixes: [] };
stashGear[4] = { id: Math.random(), base: "Heavy Belt", name: "Heavy Belt", slot: "belt", rarity: "gray", baseArmour: 25, prefixes: [], suffixes: [] };

stashOrgans[0] = { id: Math.random(), type: "organ_liver", name: "Festering Liver" };
stashOrgans[1] = { id: Math.random(), type: "organ_heart", name: "Pulsing Heart" };

[...ACTIVE_SKILL_GEMS, ...SUPPORT_GEMS].forEach((gem, i) => {
  if (i < STASH_SIZE) stashGems[i] = gem;
});

let benchItem = null;
let benchOrgan = null;

// Entities & Ground Effects
const enemies = [];
const playerMinions = [];
const particles = [];
const projectiles = [];
const drops = [];
const groundEffects = [];

// SKELETAL NECROMANCER BOSS (Malok)
const boss = {
  active: true,
  name: "Malok, The Skeletal Necromancer",
  x: 2250,
  y: 2250,
  radius: 28,
  speed: 1.1,
  hp: 4500,
  maxHp: 4500,
  fireRes: 0.30,
  coldRes: 0.30,
  lightningRes: 0.30,
  burnTimer: 0,
  burnDps: 0,
  poisonTimer: 0,
  poisonDps: 0,
  boneShield: false,
  boneShieldTimer: 0,
  boneShieldCooldown: 600,
  chaosBoltCooldown: 120,
  summonCooldown: 400,
  walkTimer: 0,
  angle: 0
};

// WORLD MAP PROPS & ROADS
const worldRoads = [
  { x1: 450, y1: 500, x2: 2250, y2: 2250, width: 44 },
  { x1: 2250, y1: 500, x2: 2250, y2: 4000, width: 40 },
  { x1: 500, y1: 2250, x2: 4000, y2: 2250, width: 40 }
];

const obstacles = [];
for (let i = 0; i < 8; i++) {
  const ang = (i / 8) * Math.PI * 2;
  obstacles.push({ type: "pillar", x: 2250 + Math.cos(ang) * 340, y: 2250 + Math.sin(ang) * 340, radius: 26 });
}
const cottagePositions = [
  { x: 380, y: 320, w: 140, h: 90 },
  { x: 750, y: 480, w: 120, h: 80 },
  { x: 1200, y: 1300, w: 150, h: 100 },
  { x: 1800, y: 2100, w: 140, h: 90 },
  { x: 2600, y: 2400, w: 160, h: 100 },
  { x: 3100, y: 1800, w: 130, h: 85 },
  { x: 1500, y: 3200, w: 140, h: 90 },
  { x: 3400, y: 3300, w: 150, h: 95 }
];
cottagePositions.forEach(c => obstacles.push({ type: "house", ...c }));

for (let i = 0; i < 110; i++) {
  obstacles.push({
    type: "tree",
    x: Math.random() * (WORLD_WIDTH - 240) + 120,
    y: Math.random() * (WORLD_HEIGHT - 240) + 120,
    radius: 25, canopyR: 54
  });
}
for (let i = 0; i < 65; i++) {
  obstacles.push({
    type: "rock",
    x: Math.random() * (WORLD_WIDTH - 240) + 120,
    y: Math.random() * (WORLD_HEIGHT - 240) + 120,
    radius: 22
  });
}

// Mouse Tracking
let mouseScreenX = width / 2, mouseScreenY = height / 2;
let mouseWorldX = hero.x, mouseWorldY = hero.y;

canvas.addEventListener('mousemove', e => {
  const rect = canvas.getBoundingClientRect();
  mouseScreenX = e.clientX - rect.left;
  mouseScreenY = e.clientY - rect.top;
});
canvas.addEventListener('contextmenu', e => {
  e.preventDefault();
  triggerActionSlot(1);
});
canvas.addEventListener('mousedown', e => {
  if (e.button === 0) performCleave();
});

const keys = {};
window.addEventListener('keydown', e => {
  keys[e.key.toLowerCase()] = true;
  if (e.key.toLowerCase() === 'p' || e.key === 'Escape') {
    if (isTreeOpen) toggleTalentTree();
    else togglePause();
  }
  if (e.key.toLowerCase() === 'u') toggleTalentTree();
  if (e.key.toLowerCase() === 'q') triggerActionSlot(2);
  if (e.key.toLowerCase() === 'w') triggerActionSlot(3);
  if (e.key.toLowerCase() === 'e') triggerActionSlot(4);
  if (e.key.toLowerCase() === 'r') triggerActionSlot(5);
  if (e.key.toLowerCase() === 't') triggerActionSlot(6);
  if (e.code === 'Space') performCleave();
});
window.addEventListener('keyup', e => keys[e.key.toLowerCase()] = false);

function togglePause() {
  if (hero.isDead || isTreeOpen) return;
  isPaused = !isPaused;
  document.getElementById('pause-overlay').style.display = isPaused ? 'block' : 'none';
  const btn = document.getElementById('pause-btn');
  btn.innerText = isPaused ? 'RESUME [P]' : '[P] PAUSE / CRAFT';
  btn.classList.toggle('paused', isPaused);
}

function resurrectHero() {
  hero.hp = hero.maxHp;
  hero.mana = hero.maxMana;
  hero.rage = 0;
  hero.x = 500;
  hero.y = 500;
  hero.isDead = false;
  document.getElementById('death-screen').style.display = 'none';
  renderD2Globes();
}

function gainExperience(amount) {
  if (hero.level >= 100) return;
  hero.exp += amount;

  while (hero.exp >= hero.expToNext && hero.level < 100) {
    hero.exp -= hero.expToNext;
    hero.level++;
    hero.talentPoints += 5;
    playSound('levelup');
    createFloatingText(hero.x, hero.y - 35, `LEVEL UP! (${hero.level}) +5 POINTS!`, "#ffd748");
    hero.expToNext = Math.round(100 * Math.pow(hero.level, 1.62) + hero.level * 40);
  }

  updateExpHUD();
  document.getElementById('tree-points-badge').innerText = hero.talentPoints;
}

function updateExpHUD() {
  document.getElementById('lvl-val').innerText = hero.level;
  document.getElementById('exp-val').innerText = hero.level >= 100 ? "MAX" : `${Math.round(hero.exp)}/${hero.expToNext}`;

  const pct = hero.level >= 100 ? 1 : (hero.exp / hero.expToNext);
  for (let i = 0; i < 10; i++) {
    const seg = document.getElementById(`exp-seg-${i}`);
    const segStart = i * 0.1;
    const segEnd = (i + 1) * 0.1;
    if (pct >= segEnd) seg.style.width = "100%";
    else if (pct > segStart) seg.style.width = `${((pct - segStart) / 0.1) * 100}%`;
    else seg.style.width = "0%";
  }
}

// --- 800-NODE PASSIVE TREE ENGINE ---
const TOTAL_TREE_NODES = 800;
const treeNodes = [];
const treeEdges = [];
let isTreeOpen = false;

function initPoEPassiveTree() {
  treeNodes.length = 0;
  treeEdges.length = 0;

  treeNodes.push({
    id: 0,
    x: 0, y: 0,
    type: "origin",
    name: "Ascendant Core",
    desc: "Origin of all paths across the 8 cardinal passive sectors.",
    cost: 0,
    isBig: false,
    allocated: true,
    neighbors: []
  });

  function addEdge(n1, n2) {
    if (n1 === n2 || n1 == null || n2 == null) return;
    if (!treeNodes[n1].neighbors.includes(n2)) treeNodes[n1].neighbors.push(n2);
    if (!treeNodes[n2].neighbors.includes(n1)) treeNodes[n2].neighbors.push(n1);

    const minId = Math.min(n1, n2);
    const maxId = Math.max(n1, n2);
    if (!treeEdges.some(e => e.u === minId && e.v === maxId)) {
      treeEdges.push({ u: minId, v: maxId });
    }
  }

  const SECTORS = 8;
  let nextId = 1;

  const sectorConfigs = [
    { name: "Wrath", type: "keystone_wrath", desc: "KEYSTONE: Adds +1 Physical Damage per 5 Strength." },
    { name: "Abomination", type: "keystone_abomination", desc: "KEYSTONE: Adds +100 HP per 5 Strength." },
    { name: "Iron Grip", type: "keystone_iron_grip", desc: "KEYSTONE: Allows wielding 2-Handed weapons in off-hand." },
    { name: "Deadly Mastery", type: "keystone_deadly_mastery", desc: "KEYSTONE: Adds +1 Physical Damage per 5 Dexterity." },
    { name: "Primal Savagery", type: "notable_rage", desc: "RAGE NOTABLE: +3 to Maximum Rage (Unlocks Yellow Rage Bar)." },
    { name: "Heart of the Colossus", type: "hp", desc: "NOTABLE: +120 Maximum Life and +10% Armor." },
    { name: "Arcane Might", type: "keystone_arcane_might", desc: "KEYSTONE: Adds +1 Spell Damage per 5 Mana." },
    { name: "Eldritch Flesh", type: "mana", desc: "NOTABLE: +80 Maximum Mana and +50 Maximum Life." }
  ];

  const sectorSpines = [];

  for (let s = 0; s < SECTORS; s++) {
    sectorSpines[s] = [];
    const baseAngle = s * (Math.PI / 4);

    let prevSpine = 0;
    const spineDists = [120, 200, 300, 430, 550, 680, 800, 950];
    for (let step = 0; step < 8; step++) {
      const dist = spineDists[step];
      const nx = Math.cos(baseAngle) * dist;
      const ny = Math.sin(baseAngle) * dist;

      let type = (s % 3 === 0) ? "str" : (s % 3 === 1) ? "dex" : "int";
      let name = type === "str" ? "+10 Strength" : type === "dex" ? "+10 Dexterity" : "+10 Intelligence";
      let desc = type === "str" ? "+10 Strength (10 Str = +1 Life)" : type === "dex" ? "+10 Dexterity (+0.001% Atk Spd, +1 Phys)" : "+10 Intelligence (10 Int = +1 Mana)";

      const nodeObj = {
        id: nextId,
        x: nx, y: ny,
        type: type,
        name: name,
        desc: desc,
        cost: 1,
        isBig: false,
        allocated: false,
        neighbors: []
      };

      treeNodes.push(nodeObj);
      addEdge(prevSpine, nextId);
      sectorSpines[s].push(nextId);
      prevSpine = nextId;
      nextId++;
    }

    function buildClusterWheel(anchorSpineNodeId, centerDist, angleOffset, clusterRadius, count, apexNotable) {
      const cx = Math.cos(baseAngle + angleOffset) * centerDist;
      const cy = Math.sin(baseAngle + angleOffset) * centerDist;
      const wheelIds = [];

      for (let i = 0; i < count; i++) {
        if (nextId >= TOTAL_TREE_NODES) break;

        const ang = (i / count) * Math.PI * 2;
        const wx = cx + Math.cos(ang) * clusterRadius;
        const wy = cy + Math.sin(ang) * clusterRadius;

        const isApex = apexNotable && (i === Math.floor(count / 2));
        let nType = "str", nName = "+10 Strength", nDesc = "+10 Strength", nCost = 1, nBig = false;

        if (isApex) {
          nCost = 5;
          nBig = true;
          const conf = sectorConfigs[s];
          nType = conf.type;
          nName = conf.name;
          nDesc = conf.desc;
        } else {
          const r = nextId % 5;
          if (r === 0) { nType = "str"; nName = "+10 Strength"; nDesc = "+10 Strength (10 Str = +1 Life)"; }
          else if (r === 1) { nType = "dex"; nName = "+10 Dexterity"; nDesc = "+10 Dexterity (+0.001% Atk Spd, +1 Phys)"; }
          else if (r === 2) { nType = "int"; nName = "+10 Intelligence"; nDesc = "+10 Intelligence (10 Int = +1 Mana)"; }
          else if (r === 3) { nType = "hp"; nName = "+20 Maximum Life"; nDesc = "+20 to Maximum Hitpoints"; }
          else { nType = "mana"; nName = "+20 Maximum Mana"; nDesc = "+20 to Maximum Mana Pool"; }
        }

        const nodeObj = {
          id: nextId,
          x: wx, y: wy,
          type: nType,
          name: nName,
          desc: nDesc,
          cost: nCost,
          isBig: nBig,
          allocated: false,
          neighbors: []
        };

        treeNodes.push(nodeObj);
        wheelIds.push(nextId);
        nextId++;
      }

      for (let i = 0; i < wheelIds.length; i++) {
        addEdge(wheelIds[i], wheelIds[(i + 1) % wheelIds.length]);
      }

      if (wheelIds.length > 0) {
        let bestIdx = 0;
        let bestDist = Infinity;
        const aNode = treeNodes[anchorSpineNodeId];
        wheelIds.forEach((wId, idx) => {
          const wNode = treeNodes[wId];
          const d = Math.hypot(wNode.x - aNode.x, wNode.y - aNode.y);
          if (d < bestDist) { bestDist = d; bestIdx = idx; }
        });
        addEdge(anchorSpineNodeId, wheelIds[bestIdx]);
      }

      return wheelIds;
    }

    buildClusterWheel(sectorSpines[s][1], 200, -0.16, 24, 6, false);
    buildClusterWheel(sectorSpines[s][1], 200, 0.16, 24, 6, false);

    buildClusterWheel(sectorSpines[s][3], 430, -0.14, 28, 8, false);
    buildClusterWheel(sectorSpines[s][3], 430, 0.14, 28, 8, false);

    buildClusterWheel(sectorSpines[s][5], 680, -0.13, 30, 9, false);
    buildClusterWheel(sectorSpines[s][5], 680, 0.13, 30, 9, false);

    buildClusterWheel(sectorSpines[s][7], 1025, 0, 36, 13, true);

    buildClusterWheel(sectorSpines[s][7], 1150, -0.12, 26, 8, false);
    buildClusterWheel(sectorSpines[s][7], 1150, 0.12, 26, 8, false);
  }

  const ringConfigs = [
    { spineIdx: 2, radius: 300, nodeCount: 3 },
    { spineIdx: 4, radius: 550, nodeCount: 6 },
    { spineIdx: 6, radius: 800, nodeCount: 8 }
  ];

  ringConfigs.forEach(rc => {
    for (let s = 0; s < SECTORS; s++) {
      const nextSec = (s + 1) % SECTORS;
      const startSpineNode = sectorSpines[s][rc.spineIdx];
      const endSpineNode = sectorSpines[nextSec][rc.spineIdx];

      const angStart = s * (Math.PI / 4);
      const angEnd = (s + 1) * (Math.PI / 4);

      let prevArcId = startSpineNode;
      const actualCount = (rc.spineIdx === 6 && s === 7) ? (rc.nodeCount - 1) : rc.nodeCount;

      for (let i = 1; i <= actualCount; i++) {
        if (nextId >= TOTAL_TREE_NODES) break;

        const t = i / (actualCount + 1);
        const arcAng = angStart + t * (angEnd - angStart);
        const ax = Math.cos(arcAng) * rc.radius;
        const ay = Math.sin(arcAng) * rc.radius;

        const nodeObj = {
          id: nextId,
          x: ax, y: ay,
          type: (rc.spineIdx === 2) ? "hp" : (rc.spineIdx === 4) ? "mana" : "str",
          name: (rc.spineIdx === 2) ? "+20 Maximum Life" : (rc.spineIdx === 4) ? "+20 Maximum Mana" : "+10 Strength",
          desc: "Highway Gateway Path",
          cost: 1,
          isBig: false,
          allocated: false,
          neighbors: []
        };

        treeNodes.push(nodeObj);
        addEdge(prevArcId, nextId);
        prevArcId = nextId;
        nextId++;
      }

      addEdge(prevArcId, endSpineNode);
    }
  });

  while (nextId < TOTAL_TREE_NODES) {
    const attachNode = treeNodes[nextId - 1];
    const nodeObj = {
      id: nextId,
      x: attachNode.x + 35,
      y: attachNode.y + 35,
      type: "hp",
      name: "+20 Maximum Life",
      desc: "Path of Vitality",
      cost: 1,
      isBig: false,
      allocated: false,
      neighbors: []
    };
    treeNodes.push(nodeObj);
    addEdge(attachNode.id, nextId);
    nextId++;
  }
}
initPoEPassiveTree();

// Interactive Tree Pan, Zoom & Universal Hover Tooltip
const treeCanvas = document.getElementById('tree-canvas');
const treeCtx = treeCanvas.getContext('2d');
let treePanX = 0, treePanY = 0;
let treeZoom = 0.85;
let isDraggingTree = false;
let lastMouseX = 0, lastMouseY = 0;
let hoveredTreeNode = null;

function resizeTreeCanvas() {
  treeCanvas.width = document.getElementById('tree-canvas-container').clientWidth;
  treeCanvas.height = document.getElementById('tree-canvas-container').clientHeight;
}
window.addEventListener('resize', resizeTreeCanvas);

const treeContainer = document.getElementById('tree-canvas-container');
const treeTooltipEl = document.getElementById('tree-tooltip');
const ttTName = document.getElementById('tt-tname');
const ttTCost = document.getElementById('tt-tcost');
const ttTDesc = document.getElementById('tt-tdesc');

treeContainer.addEventListener('mousedown', e => {
  if (e.button === 0) {
    isDraggingTree = true;
    lastMouseX = e.clientX;
    lastMouseY = e.clientY;
  }
});

treeContainer.addEventListener('mousemove', e => {
  if (isDraggingTree) {
    treePanX += (e.clientX - lastMouseX);
    treePanY += (e.clientY - lastMouseY);
    lastMouseX = e.clientX;
    lastMouseY = e.clientY;
    drawTalentTree();
  }

  const rect = treeCanvas.getBoundingClientRect();
  const clickX = e.clientX - rect.left;
  const clickY = e.clientY - rect.top;
  const worldX = (clickX - treeCanvas.width / 2 - treePanX) / treeZoom;
  const worldY = (clickY - treeCanvas.height / 2 - treePanY) / treeZoom;

  hoveredTreeNode = treeNodes.find(n => Math.hypot(n.x - worldX, n.y - worldY) < (n.isBig ? 20 : 12));
  if (hoveredTreeNode) {
    ttTName.innerText = hoveredTreeNode.name;
    ttTCost.innerText = `Cost: ${hoveredTreeNode.cost} Point${hoveredTreeNode.cost > 1 ? 's' : ''} ${hoveredTreeNode.allocated ? '• [ALLOCATED]' : ''}`;
    ttTDesc.innerText = hoveredTreeNode.desc;
    treeTooltipEl.style.display = 'block';
    treeTooltipEl.style.left = (clickX + 16) + 'px';
    treeTooltipEl.style.top = (clickY + 16) + 'px';
  } else {
    treeTooltipEl.style.display = 'none';
  }
});

window.addEventListener('mouseup', () => { isDraggingTree = false; });
treeContainer.addEventListener('mouseleave', () => { treeTooltipEl.style.display = 'none'; });

treeContainer.addEventListener('wheel', e => {
  e.preventDefault();
  const zoomFactor = e.deltaY < 0 ? 1.15 : 0.88;
  treeZoom = Math.max(0.2, Math.min(2.5, treeZoom * zoomFactor));
  drawTalentTree();
});

treeContainer.addEventListener('click', e => {
  if (hoveredTreeNode && !hoveredTreeNode.allocated) {
    const isAdjacentAllocated = hoveredTreeNode.neighbors.some(nId => treeNodes[nId] && treeNodes[nId].allocated);
    if (!isAdjacentAllocated) {
      alert("Must path through connected adjacent allocated nodes from Center Origin!");
      return;
    }

    if (hero.talentPoints < hoveredTreeNode.cost) {
      alert(`Not enough skill points! Requires ${hoveredTreeNode.cost} Point${hoveredTreeNode.cost > 1 ? 's' : ''}.`);
      return;
    }

    hero.talentPoints -= hoveredTreeNode.cost;
    hoveredTreeNode.allocated = true;
    playSound('talent');
    recalculateStats();
    drawTalentTree();
  }
});

function toggleTalentTree() {
  isTreeOpen = !isTreeOpen;
  document.getElementById('talent-modal').style.display = isTreeOpen ? 'flex' : 'none';
  if (isTreeOpen) {
    resizeTreeCanvas();
    drawTalentTree();
  } else {
    treeTooltipEl.style.display = 'none';
  }
}

function drawTalentTree() {
  if (!isTreeOpen) return;
  treeCtx.clearRect(0, 0, treeCanvas.width, treeCanvas.height);

  treeCtx.save();
  treeCtx.translate(treeCanvas.width / 2 + treePanX, treeCanvas.height / 2 + treePanY);
  treeCtx.scale(treeZoom, treeZoom);

  treeEdges.forEach(edge => {
    const n1 = treeNodes[edge.u];
    const n2 = treeNodes[edge.v];
    const isAllocatedPath = n1.allocated && n2.allocated;

    treeCtx.beginPath();
    treeCtx.moveTo(n1.x, n1.y);
    treeCtx.lineTo(n2.x, n2.y);

    if (isAllocatedPath) {
      treeCtx.strokeStyle = "#ffe54c";
      treeCtx.lineWidth = 3.2;
      treeCtx.shadowColor = "#ffe54c";
      treeCtx.shadowBlur = 8;
    } else {
      treeCtx.strokeStyle = "rgba(184, 148, 40, 0.45)";
      treeCtx.lineWidth = 1.6;
      treeCtx.shadowBlur = 0;
    }
    treeCtx.stroke();
  });
  treeCtx.shadowBlur = 0;

  treeNodes.forEach(node => {
    const isOrigin = node.type === "origin";
    const isBig = node.isBig;
    const radius = isOrigin ? 18 : (isBig ? 14 : 6.5);

    let baseColor = "#8a8a8a";
    if (node.type === "str") baseColor = "#f87171";
    else if (node.type === "dex") baseColor = "#4ade80";
    else if (node.type === "int") baseColor = "#60a5fa";
    else if (node.type === "hp") baseColor = "#ef4444";
    else if (node.type === "mana") baseColor = "#38bdf8";
    else if (node.type.startsWith("keystone_")) baseColor = "#ffd748";
    else if (node.type.startsWith("notable_")) baseColor = "#fbbf24";

    treeCtx.beginPath();
    treeCtx.arc(node.x, node.y, radius, 0, Math.PI * 2);
    treeCtx.fillStyle = node.allocated ? baseColor : "#090f0a";
    treeCtx.fill();

    treeCtx.strokeStyle = node.allocated ? "#ffffff" : (isBig ? "#ffd748" : baseColor);
    treeCtx.lineWidth = node.allocated ? 2.5 : 1.4;
    treeCtx.stroke();

    if (isBig || isOrigin) {
      treeCtx.beginPath();
      treeCtx.arc(node.x, node.y, radius + 4, 0, Math.PI * 2);
      treeCtx.strokeStyle = node.allocated ? "#ffe54c" : "rgba(255, 215, 72, 0.5)";
      treeCtx.lineWidth = 1.5;
      treeCtx.stroke();
    }
  });

  treeCtx.restore();

  document.getElementById('summary-points').innerText = hero.talentPoints;
  document.getElementById('summary-str').innerText = hero.strength;
  document.getElementById('summary-int').innerText = hero.intelligence;
  document.getElementById('summary-dex').innerText = hero.dexterity;
  document.getElementById('summary-rage').innerText = `${Math.round(hero.rage)}/${hero.maxRage}`;
  document.getElementById('tree-points-badge').innerText = hero.talentPoints;
}

// --- STAT RECALCULATION & ELEMENTAL DEFENSES ---
function recalculateStats() {
  let totalStr = 0, totalInt = 0, totalDex = 0;
  let talentHp = 0, talentMana = 0, maxRage = 0;
  let hasRage = false;

  hero.ironGrip = false;
  hero.arcaneMight = false;
  hero.wrath = false;
  hero.deadlyMastery = false;
  hero.abomination = false;

  treeNodes.forEach(n => {
    if (n.allocated) {
      if (n.type === "str") totalStr += 10;
      else if (n.type === "dex") totalDex += 10;
      else if (n.type === "int") totalInt += 10;
      else if (n.type === "hp") talentHp += 20;
      else if (n.type === "mana") talentMana += 20;
      else if (n.type === "notable_rage") { maxRage += 3; hasRage = true; }
      else if (n.type === "keystone_iron_grip") hero.ironGrip = true;
      else if (n.type === "keystone_arcane_might") hero.arcaneMight = true;
      else if (n.type === "keystone_wrath") hero.wrath = true;
      else if (n.type === "keystone_deadly_mastery") hero.deadlyMastery = true;
      else if (n.type === "keystone_abomination") hero.abomination = true;
    }
  });

  hero.strength = totalStr;
  hero.intelligence = totalInt;
  hero.dexterity = totalDex;
  hero.hasRage = hasRage;
  hero.maxRage = maxRage;

  document.getElementById('rage-bar-wrapper').style.display = hasRage ? 'block' : 'none';

  let equipHp = 0, equipArmour = 0;
  let flatPhys = 0, flatFire = 0, flatCold = 0, flatLightning = 0;
  let fireRes = 0.0, coldRes = 0.0, lightningRes = 0.0;
  let dotMultiplier = 0.0;
  let critChanceBonus = 0.0, critMult = 1.5, atkSpd = 1.0;

  Object.values(equipped).forEach(item => {
    if (!item) return;
    if (item.baseArmour) equipArmour += item.baseArmour;

    const allAffixes = [...(item.prefixes || []), ...(item.suffixes || [])];
    allAffixes.forEach(aff => {
      if (aff.key === 'life') equipHp += aff.val;
      if (aff.key === 'armour') equipArmour += aff.val;
      if (aff.key === 'flat_phys') flatPhys += aff.val;
      if (aff.key === 'flat_fire') flatFire += aff.val;
      if (aff.key === 'flat_cold') flatCold += aff.val;
      if (aff.key === 'flat_lightning') flatLightning += aff.val;
      if (aff.key === 'crit_mult') critMult += aff.val;
      if (aff.key === 'fire_res') fireRes += aff.val;
      if (aff.key === 'cold_res') coldRes += aff.val;
      if (aff.key === 'lightning_res') lightningRes += aff.val;
      if (aff.key === 'atk_spd') atkSpd += aff.val;
      if (aff.key === 'dot_mult') dotMultiplier += aff.val;
      if (aff.key === 'crit_chance') {
        critChanceBonus += (aff.val * 0.10);
      }
      if (aff.key === 'str') totalStr += aff.val;
      if (aff.key === 'dex') totalDex += aff.val;
      if (aff.key === 'int') totalInt += aff.val;
    });
  });

  const strHpBonus = Math.floor(totalStr / 10) * 1 + (hero.abomination ? Math.floor(totalStr / 5) * 100 : 0);
  const intManaBonus = Math.floor(totalInt / 10) * 1;
  const dexAtkSpdBonus = (Math.floor(totalDex / 10) * 0.00001);
  const dexPhysBonus = Math.floor(totalDex / 10) * 1;
  const wrathBonus = hero.wrath ? Math.floor(totalStr / 5) * 1 : 0;
  const deadlyBonus = hero.deadlyMastery ? Math.floor(totalDex / 5) * 1 : 0;

  hero.maxHp = 1000 + equipHp + talentHp + strHpBonus;
  hero.hp = Math.min(hero.hp, hero.maxHp);
  hero.maxMana = 250 + talentMana + intManaBonus;
  hero.mana = Math.min(hero.mana, hero.maxMana);

  // Defenses (60% Max Cap)
  hero.armour = equipArmour;
  hero.fireRes = Math.min(0.60, fireRes);
  hero.coldRes = Math.min(0.60, coldRes);
  hero.lightningRes = Math.min(0.60, lightningRes);
  hero.dotMultiplier = dotMultiplier;

  // Offensive stats
  hero.flatPhys = flatPhys;
  hero.flatFire = flatFire;
  hero.flatCold = flatCold;
  hero.flatLightning = flatLightning;
  hero.critChance = Math.min(0.95, 0.05 + critChanceBonus);
  hero.critMult = critMult;
  hero.attackSpeedMult = atkSpd + dexAtkSpdBonus;
  hero.bonusFlatPhys = dexPhysBonus + wrathBonus + deadlyBonus;

  document.getElementById('armour-val').innerText = hero.armour;
  document.getElementById('fres-val').innerText = `${Math.round(hero.fireRes * 100)}%`;
  document.getElementById('cres-val').innerText = `${Math.round(hero.coldRes * 100)}%`;
  document.getElementById('lres-val').innerText = `${Math.round(hero.lightningRes * 100)}%`;

  renderD2Globes();
}

function resolveCollisions(entity) {
  obstacles.forEach(obs => {
    if (obs.type === "house") {
      const closestX = Math.max(obs.x, Math.min(entity.x, obs.x + obs.w));
      const closestY = Math.max(obs.y, Math.min(entity.y, obs.y + obs.h));
      const dx = entity.x - closestX;
      const dy = entity.y - closestY;
      const dist = Math.hypot(dx, dy);
      if (dist < entity.radius) {
        const overlap = entity.radius - dist;
        entity.x += (dx / (dist || 1)) * overlap;
        entity.y += (dy / (dist || 1)) * overlap;
      }
    } else {
      const dx = entity.x - obs.x;
      const dy = entity.y - obs.y;
      const dist = Math.hypot(dx, dy);
      const minDist = entity.radius + obs.radius;
      if (dist < minDist) {
        const overlap = minDist - dist;
        entity.x += (dx / (dist || 1)) * overlap;
        entity.y += (dy / (dist || 1)) * overlap;
      }
    }
  });
}

// --- CONTINUOUS MONSTER SPAWNING (NO MAXIMUM LIMIT PER LEVEL) ---
function spawnWaveEnemy() {
  if (isPaused || isTreeOpen) return;

  const ang = Math.random() * Math.PI * 2;
  const dist = 600 + Math.random() * 300;
  const x = Math.max(80, Math.min(WORLD_WIDTH - 80, hero.x + Math.cos(ang) * dist));
  const y = Math.max(80, Math.min(WORLD_HEIGHT - 80, hero.y + Math.sin(ang) * dist));

  const roll = Math.random();
  let enemyType = "skeleton";

  if (roll < 0.35) enemyType = "skeleton";
  else if (roll < 0.65) enemyType = "zombie";
  else if (roll < 0.77) enemyType = "pyromancer";
  else if (roll < 0.89) enemyType = "cryomancer";
  else enemyType = "electromancer";

  const isCaster = ["pyromancer", "cryomancer", "electromancer"].includes(enemyType);

  // Progressive Wave HP & Damage Multipliers
  const waveHpMult = 1 + (currentWave - 1) * 0.18;
  const waveDmgMult = 1 + (currentWave - 1) * 0.12;

  const baseHp = isCaster ? 120 : (enemyType === "skeleton" ? 80 : 180);
  const scaledHp = Math.round(baseHp * waveHpMult);

  enemies.push({
    type: enemyType,
    x, y,
    radius: isCaster ? 16 : (enemyType === "skeleton" ? 15 : 18),
    speed: isCaster ? 1.2 : (enemyType === "skeleton" ? 1.4 : 1.0),
    hp: scaledHp,
    maxHp: scaledHp,
    dmgMult: waveDmgMult,
    fireRes: enemyType === "pyromancer" ? 0.40 : 0.0,
    coldRes: enemyType === "cryomancer" ? 0.40 : 0.0,
    lightningRes: enemyType === "electromancer" ? 0.40 : 0.0,
    burnTimer: 0,
    burnDps: 0,
    poisonTimer: 0,
    poisonDps: 0,
    attackCooldown: Math.floor(Math.random() * 40),
    walkTimer: Math.random() * 10
  });
}

// Continuous spawning cadence accelerates on higher waves
setInterval(spawnWaveEnemy, Math.max(450, 950 - currentWave * 15));

// --- 1-MINUTE SURVIVAL TIMER ENGINE ---
function startWave(waveNum) {
  currentWave = Math.min(MAX_WAVES, waveNum);
  waveSecondsLeft = WAVE_DURATION_SECONDS;

  // Boss comes 50% stronger each wave!
  const bossScale = Math.pow(1.5, currentWave - 1);
  boss.active = true;
  boss.maxHp = Math.round(4500 * bossScale);
  boss.hp = boss.maxHp;
  boss.dmgMult = bossScale;
  boss.x = 2250;
  boss.y = 2250;
  boss.boneShield = false;
  boss.boneShieldTimer = 0;
  boss.boneShieldCooldown = 600;

  createFloatingText(hero.x, hero.y - 45, `WAVE ${currentWave} BEGINS! SURVIVE 1 MINUTE!`, "#38bdf8");
  updateWaveHUD();

  if (waveTimerInterval) clearInterval(waveTimerInterval);
  waveTimerInterval = setInterval(() => {
    if (!isPaused && !isTreeOpen && !hero.isDead) {
      waveSecondsLeft--;
      updateWaveHUD();

      // Surviving the 1 minute advances to next wave!
      if (waveSecondsLeft <= 0) {
        completeWaveSurvival();
      }
    }
  }, 1000);
}

function completeWaveSurvival() {
  clearInterval(waveTimerInterval);
  playSound('levelup');
  gainExperience(300 + currentWave * 50);

  if (currentWave < MAX_WAVES) {
    createFloatingText(hero.x, hero.y - 45, `SURVIVED WAVE ${currentWave}! NEXT WAVE INCOMING!`, "#ffd748");
    setTimeout(() => {
      startWave(currentWave + 1);
    }, 2500);
  } else {
    createFloatingText(hero.x, hero.y - 45, "CONQUERED ALL 100 WAVES! SUPREME SURVIVOR!", "#ffd748");
  }
}

function updateWaveHUD() {
  document.getElementById('wave-val').innerText = `${currentWave} / ${MAX_WAVES}`;
  const mins = Math.floor(waveSecondsLeft / 60);
  const secs = waveSecondsLeft % 60;
  document.getElementById('wave-timer-val').innerText = `${String(mins).padStart(2, '0')}:${String(secs).padStart(2, '0')}`;
}

// --- COMBAT & DAMAGE RESOLUTION ---
function performCleave() {
  if (isPaused || isTreeOpen || hero.isDead || hero.swingCooldown > 0) return;

  const wType = getEquippedWeaponType();
  const gmpSupport = chestSockets.some(s => s && s.id === "sup_5");
  hero.swingCooldown = Math.max(8, Math.round(18 / hero.attackSpeedMult));
  hero.swingAngle = Math.atan2(mouseWorldY - hero.y, mouseWorldX - hero.x);

  if (wType === "bow") {
    playSound('bow');
    const arrowCount = gmpSupport ? 5 : 1;
    const spreadAngle = gmpSupport ? 0.38 : 0;

    for (let i = 0; i < arrowCount; i++) {
      const angleOffset = arrowCount > 1 ? (i / (arrowCount - 1) - 0.5) * spreadAngle : 0;
      const finalAngle = hero.swingAngle + angleOffset;
      projectiles.push({
        type: "hero_arrow",
        x: hero.x, y: hero.y,
        vx: Math.cos(finalAngle) * 8.5,
        vy: Math.sin(finalAngle) * 8.5,
        radius: 6,
        damageObj: {
          phys: (85 * hero.physMult + hero.bonusFlatPhys + hero.flatPhys) * (gmpSupport ? 0.85 : 1.0),
          fire: hero.flatFire,
          cold: hero.flatCold,
          lightning: hero.flatLightning
        },
        life: 55
      });
    }
    return;
  }

  playSound('slash');
  const attackReach = 88;
  const allTargets = [...enemies, ...(boss.active ? [boss] : [])];

  allTargets.forEach((e) => {
    const dx = e.x - hero.x;
    const dy = e.y - hero.y;
    if (Math.hypot(dx, dy) < attackReach + e.radius) {
      damageTarget(e, {
        phys: 95 * hero.physMult + hero.bonusFlatPhys + hero.flatPhys,
        fire: hero.flatFire,
        cold: hero.flatCold,
        lightning: hero.flatLightning
      });
    }
  });
}

function damageTarget(target, dmgObj) {
  if (target === boss && boss.boneShield) {
    playSound('deflect');
    createFloatingText(boss.x, boss.y - 30, "INVULNERABLE!", "#38bdf8");
    return;
  }

  if (hero.hasRage) hero.rage = Math.min(hero.maxRage, hero.rage + 1);

  const rageDmgMult = 1 + (Math.floor(hero.rage / 10) * 0.05);
  const isCrit = Math.random() < hero.critChance;
  const critMultiplier = isCrit ? hero.critMult : 1.0;

  const fireRes = target.fireRes || 0.0;
  const coldRes = target.coldRes || 0.0;
  const lightRes = target.lightningRes || 0.0;

  const physDmg = (dmgObj.phys || 0) * rageDmgMult * critMultiplier;
  const fireDmg = (dmgObj.fire || 0) * (1 - fireRes) * rageDmgMult * critMultiplier;
  const coldDmg = (dmgObj.cold || 0) * (1 - coldRes) * rageDmgMult * critMultiplier;
  const lightDmg = (dmgObj.lightning || 0) * (1 - lightRes) * rageDmgMult * critMultiplier;

  const totalDmg = Math.round(physDmg + fireDmg + coldDmg + lightDmg);
  target.hp -= totalDmg;

  hero.hp = Math.min(hero.maxHp, hero.hp + totalDmg * 0.03);
  renderD2Globes();

  createSplatter(target.x, target.y, target.type === "skeleton" || target === boss ? "#d9d0be" : "#631818", 8);

  if (target.hp <= 0) {
    if (target === boss) killBoss();
    else killEnemy(target, enemies.indexOf(target));
  }
}

function damageHero(dmg) {
  if (hero.isDead) return;

  const armourMitigation = Math.min(0.70, hero.armour / (hero.armour + 400));
  const physHit = (dmg.phys || 0) * (1 - armourMitigation);

  const fireHit = (dmg.fire || 0) * (1 - hero.fireRes);
  const coldHit = (dmg.cold || 0) * (1 - hero.coldRes);
  const lightHit = (dmg.lightning || 0) * (1 - hero.lightningRes);

  const totalTaken = Math.max(1, Math.round(physHit + fireHit + coldHit + lightHit));
  hero.hp = Math.max(0, hero.hp - totalTaken);
  renderD2Globes();

  if (dmg.fire > 0) createSplatter(hero.x, hero.y, "#ff4500", 6);
  if (dmg.cold > 0) createSplatter(hero.x, hero.y, "#38bdf8", 6);
  if (dmg.lightning > 0) createSplatter(hero.x, hero.y, "#fde047", 6);

  if (hero.hp <= 0) {
    hero.isDead = true;
    playSound('death');
    document.getElementById('death-screen').style.display = 'flex';
  }
}

function triggerActionSlot(slotIndex) {
  if (isPaused || isTreeOpen || hero.isDead) return;
  const assigned = activeHotkeys[slotIndex];
  if (!assigned) return;

  const wType = getEquippedWeaponType();

  if (assigned.requires === "bow" && wType !== "bow") {
    createFloatingText(hero.x, hero.y - 30, "REQUIRES A BOW!", "#ef4444");
    return;
  }
  if (assigned.requires === "melee" && wType === "bow") {
    createFloatingText(hero.x, hero.y - 30, "REQUIRES MELEE WEAPON!", "#ef4444");
    return;
  }

  const cost = assigned.mana || 0;
  if (hero.mana < cost) {
    createFloatingText(hero.x, hero.y - 25, "NOT ENOUGH MANA", "#38bdf8");
    return;
  }
  hero.mana -= cost;
  renderD2Globes();

  const gemId = assigned.id;
  const gmpSupport = chestSockets.some(s => s && s.id === "sup_5");
  const fasterProj = chestSockets.some(s => s && s.id === "sup_6");
  const incAoe = chestSockets.some(s => s && s.id === "sup_11");
  const spellBonus = hero.arcaneMight ? Math.floor(hero.mana / 5) : 0;

  if (gemId === "gem_flicker") {
    let target = null;
    let minDist = 380;
    const allTargets = [...enemies, ...(boss.active ? [boss] : [])];
    allTargets.forEach(e => {
      const d = Math.hypot(e.x - mouseWorldX, e.y - mouseWorldY);
      if (d < minDist) { minDist = d; target = e; }
    });

    if (target) {
      for (let i = 0; i < 6; i++) {
        particles.push({ x: hero.x, y: hero.y, vx: (Math.random()-0.5)*4, vy: (Math.random()-0.5)*4, radius: 6, color: "#38bdf8", life: 18 });
      }
      hero.x = target.x - Math.cos(hero.swingAngle) * 22;
      hero.y = target.y - Math.sin(hero.swingAngle) * 22;
      playSound('flicker');
      damageTarget(target, {
        phys: (240 + hero.bonusFlatPhys + hero.flatPhys) * hero.physMult,
        fire: hero.flatFire,
        cold: hero.flatCold,
        lightning: hero.flatLightning
      });
    }
  } 
  else if (gemId === "gem_molten") {
    // MOLTEN STRIKE: SLAM GROUND, 6 BASE BALLS (35 WITH GMP)
    hero.slamTimer = 22;
    hero.swingCooldown = 22;
    hero.swingAngle = Math.atan2(mouseWorldY - hero.y, mouseWorldX - hero.x);
    playSound('molten');

    const slamDist = 44;
    const crackX = hero.x + Math.cos(hero.swingAngle) * slamDist;
    const crackY = hero.y + Math.sin(hero.swingAngle) * slamDist;

    groundEffects.push({
      type: "ground_crack",
      x: crackX,
      y: crackY,
      angle: hero.swingAngle,
      life: 90,
      maxLife: 90,
      lines: [
        { dx: (Math.random() - 0.5) * 16, dy: (Math.random() - 0.5) * 16 },
        { dx: Math.cos(hero.swingAngle) * 34 + (Math.random() - 0.5) * 10, dy: Math.sin(hero.swingAngle) * 34 + (Math.random() - 0.5) * 10 },
        { dx: Math.cos(hero.swingAngle + 0.7) * 26, dy: Math.sin(hero.swingAngle + 0.7) * 26 },
        { dx: Math.cos(hero.swingAngle - 0.7) * 26, dy: Math.sin(hero.swingAngle - 0.7) * 26 }
      ]
    });

    const allTargets = [...enemies, ...(boss.active ? [boss] : [])];
    allTargets.forEach(e => {
      if (Math.hypot(e.x - crackX, e.y - crackY) < 46 + e.radius) {
        damageTarget(e, {
          phys: 85 * hero.physMult + hero.bonusFlatPhys + hero.flatPhys,
          fire: hero.flatFire + 40,
          cold: 0,
          lightning: 0
        });
      }
    });

    // 6 Base Balls, 35 with GMP!
    const ballCount = gmpSupport ? 35 : 6;
    const baseSpeed = fasterProj ? (3.8 + Math.random() * 3.2) : (1.6 + Math.random() * 1.2);

    for (let i = 0; i < ballCount; i++) {
      const randAng = Math.random() * Math.PI * 2;
      projectiles.push({
        type: "molten_ball",
        x: crackX,
        y: crackY,
        vx: Math.cos(randAng) * baseSpeed,
        vy: Math.sin(randAng) * baseSpeed,
        bounces: 2,
        bounceTimer: fasterProj ? (10 + Math.floor(Math.random() * 5)) : (22 + Math.floor(Math.random() * 8)),
        radius: incAoe ? 28 : 20,
        spellDmg: spellBonus,
        fasterProj: fasterProj,
        life: fasterProj ? 40 : 75
      });
    }
  }
  else if (gemId === "gem_double") {
    performCleave();
    setTimeout(() => { performCleave(); playSound('slash'); }, 100);
  }
  else if (gemId === "gem_zombies") {
    if (playerMinions.length >= 4) return;
    playSound('summon');
    playerMinions.push({
      x: hero.x + (Math.random() * 40 - 20),
      y: hero.y + (Math.random() * 40 - 20),
      radius: 17,
      speed: 1.8,
      hp: 300,
      attackCooldown: 0,
      walkTimer: Math.random() * 10
    });
  }
  else if (gemId === "gem_toxic") {
    // TOXIC RAIN: SKY-SHOOT, ZERO HIT DAMAGE, PURE STACKING POISON (+25 DPS SCALED BY DOT MULTIPLIER)
    hero.shootingSkyTimer = 35;
    hero.swingCooldown = 35;
    playSound('bow');

    projectiles.push({
      type: "sky_arrow",
      x: hero.x,
      y: hero.y,
      vx: (Math.random() - 0.5) * 1.5,
      vy: -19,
      life: 25
    });

    const targetX = mouseWorldX;
    const targetY = mouseWorldY;
    const arrowCount = gmpSupport ? 70 : 35;

    setTimeout(() => {
      playSound('toxic');
      const spreadRadius = incAoe ? 140 : 95;

      for (let i = 0; i < arrowCount; i++) {
        const delay = Math.random() * (fasterProj ? 12 : 24);
        const destX = targetX + (Math.random() - 0.5) * spreadRadius * 2;
        const destY = targetY + (Math.random() - 0.5) * spreadRadius * 1.6;
        const startY = destY - 480 - Math.random() * 140;
        const startX = destX - 90 - Math.random() * 50;
        const flightTime = fasterProj ? 9 : 16;

        projectiles.push({
          type: "toxic_rain_arrow",
          x: startX,
          y: startY,
          destX: destX,
          destY: destY,
          vx: (destX - startX) / flightTime,
          vy: (destY - startY) / flightTime,
          delay: delay,
          spellDmg: spellBonus,
          life: flightTime + delay
        });
      }
    }, 1000);
  }
}

function killEnemy(e, index) {
  if (index >= 0) enemies.splice(index, 1);
  hero.kills++;
  createSplatter(e.x, e.y, e.type === "skeleton" || e.type === "boss" ? "#eee7db" : "#801111", 20);

  const expReward = Math.floor(Math.random() * 11) + 20;
  gainExperience(expReward);

  if (Math.random() < 0.65) {
    const isHeart = Math.random() < 0.35;
    drops.push({
      x: e.x, y: e.y,
      type: isHeart ? "heart" : "organ",
      organType: isHeart ? "organ_heart" : "organ_liver",
      name: isHeart ? "Pulsing Heart" : "Festering Liver",
      color: isHeart ? "#f43f5e" : "#ff4747"
    });
  }

  if (Math.random() < 0.5) {
    const slotKeys = Object.keys(POE_BASES);
    const chosenSlot = slotKeys[Math.floor(Math.random() * slotKeys.length)];
    const baseList = POE_BASES[chosenSlot];
    const chosenObj = baseList[Math.floor(Math.random() * baseList.length)];

    drops.push({
      x: e.x + (Math.random() * 20 - 10),
      y: e.y + (Math.random() * 20 - 10),
      type: "item",
      slot: chosenSlot === "ring" ? "ring1" : chosenSlot,
      base: chosenObj.name,
      name: chosenObj.name,
      baseArmour: chosenObj.baseArmour,
      rarity: "gray",
      prefixes: [],
      suffixes: [],
      color: "#a6a6a6"
    });
  }
}

function killBoss() {
  boss.active = false;
  document.getElementById('boss-bar-container').style.display = 'none';
  createSplatter(boss.x, boss.y, "#902090", 60);
  playSound('death');
  gainExperience(500);

  for (let i = 0; i < 3; i++) {
    drops.push({
      x: boss.x + (Math.random() * 80 - 40),
      y: boss.y + (Math.random() * 80 - 40),
      type: "heart",
      organType: "organ_heart",
      name: "Pulsing Heart",
      color: "#f43f5e"
    });
  }
  for (let i = 0; i < 3; i++) {
    drops.push({
      x: boss.x + (Math.random() * 80 - 40),
      y: boss.y + (Math.random() * 80 - 40),
      type: "organ",
      organType: "organ_liver",
      name: "Eldritch Necromancer Liver",
      color: "#ff2222"
    });
  }
  for (let i = 0; i < 3; i++) {
    const slotKeys = Object.keys(POE_BASES);
    const chosenSlot = slotKeys[Math.floor(Math.random() * slotKeys.length)];
    const chosenBase = POE_BASES[chosenSlot][0];
    drops.push({
      x: boss.x + (Math.random() * 80 - 40),
      y: boss.y + (Math.random() * 80 - 40),
      type: "item",
      slot: chosenSlot === "ring" ? "ring1" : chosenSlot,
      base: chosenBase.name,
      name: chosenBase.name,
      baseArmour: chosenBase.baseArmour,
      rarity: "gray",
      prefixes: [],
      suffixes: [],
      color: "#a6a6a6"
    });
  }
}

function createSplatter(x, y, color, count) {
  for (let i = 0; i < count; i++) {
    particles.push({
      x, y,
      vx: (Math.random() - 0.5) * 6,
      vy: (Math.random() - 0.5) * 6,
      radius: Math.random() * 3 + 1,
      color: color,
      life: 22
    });
  }
}

function createFloatingText(x, y, text, color) {
  particles.push({
    x, y,
    vx: 0,
    vy: -1,
    text: text,
    color: color,
    life: 35
  });
}

function executeCraft() {
  if (!benchItem || !benchOrgan) return;

  if (benchOrgan.type === "organ_heart" || benchOrgan.name.includes("Heart")) {
    benchItem.prefixes = [];
    benchItem.suffixes = [];

    const prefShuffled = [...PREFIX_TEMPLATES].sort(() => 0.5 - Math.random());
    for (let i = 0; i < 3; i++) {
      benchItem.prefixes.push(generateAffix(prefShuffled[i]));
    }

    const suffShuffled = [...SUFFIX_TEMPLATES].sort(() => 0.5 - Math.random());
    for (let i = 0; i < 3; i++) {
      benchItem.suffixes.push(generateAffix(suffShuffled[i]));
    }

    benchItem.rarity = "rare";
    benchItem.name = `Reforged ${benchItem.base}`;
    playSound('reforge');
    createFloatingText(hero.x, hero.y - 30, "REFORGED WITH HEART!", "#f43f5e");
  } else {
    const canPrefix = benchItem.prefixes.length < 3;
    const canSuffix = benchItem.suffixes.length < 3;

    if (!canPrefix && !canSuffix) {
      alert("Item already has 6 Affixes (3 Prefixes & 3 Suffixes)!");
      return;
    }

    const addPrefix = canPrefix && canSuffix ? Math.random() < 0.5 : canPrefix;
    if (addPrefix) {
      const avail = PREFIX_TEMPLATES.filter(pt => !benchItem.prefixes.some(p => p.key === pt.key));
      const chosen = avail[Math.floor(Math.random() * avail.length)];
      benchItem.prefixes.push(generateAffix(chosen));
    } else {
      const avail = SUFFIX_TEMPLATES.filter(st => !benchItem.suffixes.some(s => s.key === st.key));
      const chosen = avail[Math.floor(Math.random() * avail.length)];
      benchItem.suffixes.push(generateAffix(chosen));
    }

    benchItem.rarity = "rare";
    benchItem.name = `Exalted ${benchItem.base}`;
    playSound('molten');
  }

  const organIdx = stashOrgans.indexOf(benchOrgan);
  if (organIdx >= 0) stashOrgans[organIdx] = null;
  benchOrgan = null;

  recalculateStats();
  renderUI();
}

function switchStashTab(tabName) {
  activeStashTab = tabName;
  document.getElementById('tab-btn-gear').classList.toggle('active', tabName === 'gear');
  document.getElementById('tab-btn-organs').classList.toggle('active', tabName === 'organs');
  document.getElementById('tab-btn-gems').classList.toggle('active', tabName === 'gems');
  renderStashGrid();
}

function equipItemFromStash(item, slotIdx) {
  let targetSlot = item.slot;
  if (targetSlot === 'ring1' && equipped.ring1 && !equipped.ring2) targetSlot = 'ring2';

  if (hero.ironGrip && item.slot === 'mainhand' && !equipped.offhand) {
    targetSlot = 'offhand';
  }

  const currentlyEquipped = equipped[targetSlot];
  equipped[targetSlot] = item;
  stashGear[slotIdx] = currentlyEquipped;
  recalculateStats();
  renderUI();
}

function unequipSlot(slotName) {
  const item = equipped[slotName];
  if (!item) return;

  const freeIdx = stashGear.indexOf(null);
  if (freeIdx === -1) {
    alert("Stash grid is full!");
    return;
  }
  stashGear[freeIdx] = item;
  equipped[slotName] = null;
  recalculateStats();
  renderUI();
}

function socketGemFromStash(gem, stashIdx) {
  const freeSocket = chestSockets.indexOf(null);
  if (freeSocket === -1) {
    alert("All 6 sockets in Body Armour are occupied!");
    return;
  }
  chestSockets[freeSocket] = gem;
  stashGems[stashIdx] = null;
  renderUI();
}

function unsocketGem(socketIdx) {
  const gem = chestSockets[socketIdx];
  if (!gem) return;

  const freeStash = stashGems.indexOf(null);
  if (freeStash === -1) {
    alert("Gems Stash is full!");
    return;
  }
  stashGems[freeStash] = gem;
  chestSockets[socketIdx] = null;
  renderUI();
}

let activeHotkeys = {};
function updateActionBar() {
  activeHotkeys = {};
  const activeGemsInChest = chestSockets.filter(s => s && s.type === "active");

  for (let i = 1; i <= 6; i++) {
    const slotEl = document.getElementById(`bar-slot-${i}`);
    const nameEl = slotEl.querySelector('.slot-name');
    const gem = activeGemsInChest[i - 1];

    if (gem) {
      activeHotkeys[i] = gem;
      slotEl.className = "action-slot active-gem";
      nameEl.innerText = gem.name;
    } else {
      slotEl.className = "action-slot empty";
      nameEl.innerText = "Empty";
    }
  }
}

// Universal Tooltip
const tooltipEl = document.getElementById('poe-tooltip');
const ttNameEl = document.getElementById('tt-name');
const ttSubEl = document.getElementById('tt-sub');
const ttAffixesEl = document.getElementById('tt-affixes');

function showTooltip(e, item) {
  if (!item) return;
  ttNameEl.innerText = item.name;
  ttNameEl.className = `tt-name item-${item.rarity || (item.type ? 'gem-' + item.type : 'organ')}`;
  
  if (item.slot) {
    const baseArmourText = item.baseArmour ? ` • Base Armour: ${item.baseArmour}` : "";
    ttSubEl.innerText = `${item.base || item.name} (${item.slot.toUpperCase()})${baseArmourText}`;
  } else if (item.desc) {
    ttSubEl.innerText = `${item.type.toUpperCase()} GEM`;
  } else {
    ttSubEl.innerText = item.type === "organ_heart" ? "PULSING HEART (REFORGES ALL AFFIXES)" : "HARVESTED ORGAN (SLAMS 1 AFFIX)";
  }

  let affixHtml = "";
  if (item.prefixes && item.prefixes.length > 0) {
    affixHtml += `<div style="color:#ffd748; font-size:10px; margin-top:3px;">PREFIXES (${item.prefixes.length}/3):</div>`;
    item.prefixes.forEach(p => affixHtml += `<div class="tt-affix">&bull; ${p.text}</div>`);
  }
  if (item.suffixes && item.suffixes.length > 0) {
    affixHtml += `<div style="color:#ffd748; font-size:10px; margin-top:3px;">SUFFIXES (${item.suffixes.length}/3):</div>`;
    item.suffixes.forEach(s => affixHtml += `<div class="tt-affix">&bull; ${s.text}</div>`);
  }
  if (item.desc) affixHtml += `<div class="tt-affix">${item.desc}</div>`;
  ttAffixesEl.innerHTML = affixHtml;

  tooltipEl.style.display = "block";
  moveTooltip(e);
}

function moveTooltip(e) {
  tooltipEl.style.left = (e.clientX - 280) + 'px';
  tooltipEl.style.top = (e.clientY + 10) + 'px';
}

function hideTooltip() { tooltipEl.style.display = "none"; }

function renderD2Globes() {
  const hpPct = Math.max(0, Math.min(100, (hero.hp / hero.maxHp) * 100));
  const manaPct = Math.max(0, Math.min(100, (hero.mana / hero.maxMana) * 100));
  document.getElementById('life-liquid').style.height = `${hpPct}%`;
  document.getElementById('life-text').innerText = `${Math.round(hero.hp)} / ${hero.maxHp}`;
  document.getElementById('mana-liquid').style.height = `${manaPct}%`;
  document.getElementById('mana-text').innerText = `${Math.round(hero.mana)} / ${hero.maxMana}`;

  if (hero.hasRage && hero.maxRage > 0) {
    const ragePct = Math.max(0, Math.min(100, (hero.rage / hero.maxRage) * 100));
    document.getElementById('rage-bar-fill').style.width = `${ragePct}%`;
  }
}

function renderStashGrid() {
  const gridEl = document.getElementById('stash-grid');
  gridEl.innerHTML = "";

  const activeArray = activeStashTab === 'gear' ? stashGear : activeStashTab === 'organs' ? stashOrgans : stashGems;

  activeArray.forEach((item, idx) => {
    const cell = document.createElement('div');
    cell.className = `stash-cell ${item ? 'occupied' : ''} ${benchItem === item || benchOrgan === item ? 'selected' : ''}`;

    if (item) {
      const isHeart = item.type === "organ_heart" || item.name.includes("Heart");
      const rarityClass = item.rarity ? `item-${item.rarity}` : item.type ? (isHeart ? 'item-heart' : `item-gem-${item.type}`) : 'item-organ';
      cell.classList.add(rarityClass);
      cell.innerText = item.name.split(' ')[0].substring(0, 5);

      cell.onmouseenter = (e) => showTooltip(e, item);
      cell.onmousemove = (e) => moveTooltip(e);
      cell.onmouseleave = hideTooltip;

      cell.oncontextmenu = (e) => {
        e.preventDefault();
        hideTooltip();
        if (activeStashTab === 'gear') equipItemFromStash(item, idx);
        else if (activeStashTab === 'gems') socketGemFromStash(item, idx);
      };

      cell.onclick = () => {
        if (activeStashTab === 'gear') benchItem = item;
        else if (activeStashTab === 'organs') benchOrgan = item;
        renderUI();
      };
    }

    gridEl.appendChild(cell);
  });

  document.getElementById('count-gear').innerText = stashGear.filter(x => x !== null).length;
  document.getElementById('count-organs').innerText = stashOrgans.filter(x => x !== null).length;
  document.getElementById('count-gems').innerText = stashGems.filter(x => x !== null).length;
}

function renderUI() {
  Object.keys(equipped).forEach(slot => {
    const el = document.getElementById(`slot-${slot}`);
    if (!el) return;
    const item = equipped[slot];
    if (item) {
      el.className = `equip-slot filled item-${item.rarity}`;
      el.innerText = `${item.name}\n[${slot.toUpperCase()}]`;
      el.onmouseenter = (e) => showTooltip(e, item);
      el.onmousemove = (e) => moveTooltip(e);
      el.onmouseleave = hideTooltip;
    } else {
      el.className = "equip-slot";
      el.innerText = slot.toUpperCase();
      el.onmouseenter = null;
    }
  });

  const socketsContainer = document.getElementById('sockets-container');
  socketsContainer.innerHTML = "";
  chestSockets.forEach((gem, idx) => {
    const sock = document.createElement('div');
    sock.className = `socket ${gem ? (gem.type === 'active' ? 'gem-active' : 'gem-support') : ''}`;
    sock.innerText = gem ? gem.name.split(' ')[0].substring(0, 4) : `S${idx+1}`;
    sock.onclick = () => unsocketGem(idx);
    if (gem) {
      sock.onmouseenter = (e) => showTooltip(e, gem);
      sock.onmousemove = (e) => moveTooltip(e);
      sock.onmouseleave = hideTooltip;
    }
    socketsContainer.appendChild(sock);
  });

  const itemSlot = document.getElementById('bench-item-slot');
  const organSlot = document.getElementById('bench-organ-slot');
  itemSlot.innerText = benchItem ? benchItem.name : "Click Base Item";
  itemSlot.className = `bench-slot ${benchItem ? 'loaded item-' + benchItem.rarity : ''}`;

  if (benchOrgan) {
    const isHeart = benchOrgan.type === "organ_heart" || benchOrgan.name.includes("Heart");
    organSlot.innerText = benchOrgan.name;
    organSlot.className = `bench-slot loaded ${isHeart ? 'item-heart' : 'item-organ'}`;
    document.getElementById('slam-btn').innerText = isHeart ? "Reforge Item (Heart)" : "Slam Affix (Liver)";
  } else {
    organSlot.innerText = "Click Liver / Heart";
    organSlot.className = "bench-slot";
    document.getElementById('slam-btn').innerText = "Apply Organ";
  }
  document.getElementById('slam-btn').disabled = !(benchItem && benchOrgan);

  renderStashGrid();
  updateActionBar();
  renderD2Globes();
  updateExpHUD();
  updateWaveHUD();
}

// --- ARTICULATED CHARACTER RIG WITH DYNAMIC WEAPONS, SHIELD, SLAM & SKY SHOOTING ---
function drawHero(x, y, angle, isMoving, walkTimer, idleTimer, swingTimer) {
  ctx.save();
  ctx.translate(x, y);
  ctx.rotate(angle);

  const stridePhase = walkTimer;
  const torsoBob = isMoving ? Math.abs(Math.sin(stridePhase)) * 1.5 : Math.sin(idleTimer) * 0.6;
  const hipSway = isMoving ? Math.sin(stridePhase) * 1.2 : 0;

  const lStride = isMoving ? Math.sin(stridePhase) * 8.5 : 0;
  const lLift = isMoving ? Math.max(0, -Math.cos(stridePhase)) * 3 : 0;
  const rStride = isMoving ? -Math.sin(stridePhase) * 8.5 : 0;
  const rLift = isMoving ? Math.max(0, Math.cos(stridePhase)) * 3 : 0;

  function drawArmoredLeg(hipY, stride, lift, isLeft) {
    ctx.save();
    ctx.translate(-2, hipY);
    ctx.fillStyle = "#38322a";
    ctx.strokeStyle = "#171410";
    ctx.lineWidth = 1.4;
    ctx.beginPath();
    ctx.roundRect(-4, isLeft ? -4 : 0, 7, 4, 1);
    ctx.fill();
    ctx.stroke();

    ctx.fillStyle = "#5c635c";
    ctx.beginPath();
    ctx.arc(stride * 0.4, isLeft ? -2 : 2, 3 - lift * 0.2, 0, Math.PI * 2);
    ctx.fill();
    ctx.stroke();

    ctx.fillStyle = "#4a443b";
    ctx.strokeStyle = "#171410";
    ctx.lineWidth = 1.5;
    ctx.beginPath();
    ctx.moveTo(-5 + stride, isLeft ? -4.5 : 1);
    ctx.lineTo(5 + stride + lift, isLeft ? -4 : 1.5);
    ctx.lineTo(8 + stride + lift, isLeft ? -2 : 3);
    ctx.lineTo(4 + stride, isLeft ? 0 : 4.5);
    ctx.lineTo(-5 + stride, isLeft ? -0.5 : 4);
    ctx.closePath();
    ctx.fill();
    ctx.stroke();
    ctx.restore();
  }

  drawArmoredLeg(-9 + hipSway, lStride, lLift, true);
  drawArmoredLeg(9 + hipSway, rStride, rLift, false);

  // Cape
  ctx.fillStyle = "#1b2e1e";
  ctx.strokeStyle = "#0c180e";
  ctx.lineWidth = 1.5;
  const capeWave = isMoving ? Math.sin(stridePhase) * 4 : Math.sin(idleTimer * 0.8) * 1.5;
  ctx.beginPath();
  ctx.moveTo(-7, -13);
  ctx.lineTo(-24 - Math.abs(lStride * 0.4), capeWave);
  ctx.lineTo(-7, 13);
  ctx.closePath();
  ctx.fill();
  ctx.stroke();

  ctx.save();
  ctx.translate(torsoBob * 0.5, 0);

  // Cuirass
  ctx.fillStyle = "#5c635c";
  ctx.strokeStyle = "#1b211c";
  ctx.lineWidth = 1.5;
  ctx.beginPath();
  ctx.ellipse(0, 0, 13, 11, 0, 0, Math.PI * 2);
  ctx.fill();
  ctx.stroke();

  // Pauldrons
  ctx.fillStyle = "#7b857b";
  ctx.beginPath();
  ctx.ellipse(2, -12, 6, 4.5, 0.2, 0, Math.PI * 2);
  ctx.ellipse(2, 12, 6, 4.5, -0.2, 0, Math.PI * 2);
  ctx.fill();
  ctx.stroke();

  // Helmet
  ctx.fillStyle = "#434a44";
  ctx.strokeStyle = "#191f1a";
  ctx.lineWidth = 1.5;
  ctx.beginPath();
  ctx.arc(1, 0, 8.5, 0, Math.PI * 2);
  ctx.fill();
  ctx.stroke();

  // Visor
  ctx.strokeStyle = "#50fa7b";
  ctx.lineWidth = 2;
  ctx.beginPath();
  ctx.moveTo(3, -3.5);
  ctx.lineTo(8, 0);
  ctx.lineTo(3, 3.5);
  ctx.stroke();

  // DYNAMIC SHIELD ON OFF-HAND (Left Arm)
  if (equipped.offhand && (equipped.offhand.name.includes("Shield") || equipped.offhand.slot === "offhand")) {
    ctx.save();
    ctx.translate(2, -12);
    ctx.fillStyle = "#2d382d";
    ctx.strokeStyle = "#a4916a";
    ctx.lineWidth = 1.6;
    ctx.beginPath();
    ctx.moveTo(-6, -4);
    ctx.lineTo(8, -6);
    ctx.lineTo(13, 0);
    ctx.lineTo(8, 6);
    ctx.lineTo(-6, 4);
    ctx.closePath();
    ctx.fill();
    ctx.stroke();
    ctx.fillStyle = "#ffd748";
    ctx.beginPath();
    ctx.arc(3, 0, 3, 0, Math.PI * 2);
    ctx.fill();
    ctx.restore();
  }

  // DYNAMIC WEAPONS
  ctx.save();
  const isAttacking = swingTimer > 0;
  const isShootingSky = hero.shootingSkyTimer > 0;
  const isSlamming = hero.slamTimer > 0;
  const attackAngle = isAttacking ? ((18 - swingTimer) / 18) * 1.6 - 0.8 : 0.2;
  const attackThrust = isAttacking ? 14 : 0;
  const wType = getEquippedWeaponType();

  if (wType === "bow") {
    if (isShootingSky) {
      ctx.translate(10, -6);
      ctx.rotate(-Math.PI * 0.42);
    } else {
      ctx.translate(14, 4);
      ctx.rotate(isAttacking ? -0.1 : 0.2);
    }

    ctx.strokeStyle = "#8b5a2b";
    ctx.lineWidth = 2.8;
    ctx.beginPath();
    ctx.arc(0, 0, 18, -Math.PI * 0.45, Math.PI * 0.45);
    ctx.stroke();

    const pullX = (isAttacking || isShootingSky) ? -13 : -3;
    ctx.strokeStyle = isShootingSky ? "#4ade80" : "#f0f0f0";
    ctx.lineWidth = 1;
    ctx.beginPath();
    ctx.moveTo(18 * Math.cos(-Math.PI * 0.45), 18 * Math.sin(-Math.PI * 0.45));
    ctx.lineTo(pullX, 0);
    ctx.lineTo(18 * Math.cos(Math.PI * 0.45), 18 * Math.sin(Math.PI * 0.45));
    ctx.stroke();

    if (isAttacking || isShootingSky) {
      ctx.strokeStyle = isShootingSky ? "#4ade80" : "#dedede";
      ctx.lineWidth = 1.6;
      ctx.beginPath();
      ctx.moveTo(pullX, 0);
      ctx.lineTo(pullX + 24, 0);
      ctx.stroke();
      ctx.fillStyle = isShootingSky ? "#22c55e" : "#888";
      ctx.beginPath();
      ctx.moveTo(pullX + 24, -2.5);
      ctx.lineTo(pullX + 28, 0);
      ctx.lineTo(pullX + 24, 2.5);
      ctx.fill();
    }
  } else if (wType === "staff") {
    if (isSlamming) {
      ctx.translate(22, 0);
      ctx.rotate(Math.PI * 0.48);
    } else {
      ctx.translate(12 + attackThrust, 8);
      ctx.rotate(attackAngle);
    }

    ctx.fillStyle = "#4a3319";
    ctx.fillRect(-14, -2, 38, 4);
    ctx.strokeStyle = "#1e140a";
    ctx.lineWidth = 1.2;
    ctx.strokeRect(-14, -2, 38, 4);

    ctx.fillStyle = isSlamming ? "#ff6a00" : "#38bdf8";
    ctx.beginPath();
    ctx.arc(26, 0, 6, 0, Math.PI * 2);
    ctx.fill();
    ctx.strokeStyle = "#ffd748";
    ctx.lineWidth = 1.5;
    ctx.stroke();
  } else {
    if (isSlamming) {
      ctx.translate(24, 0);
      ctx.rotate(Math.PI * 0.52);
    } else {
      ctx.translate(14 + attackThrust, 10);
      ctx.rotate(attackAngle);
    }

    ctx.fillStyle = "#8c7247";
    ctx.fillRect(-3, -4, 4, 8);
    ctx.fillStyle = isSlamming ? "#ff9955" : "#e0e6ed";
    ctx.strokeStyle = "#3e464f";
    ctx.lineWidth = 1.5;
    ctx.beginPath();
    ctx.moveTo(1, -3);
    ctx.lineTo(28, -4);
    ctx.lineTo(34, 0);
    ctx.lineTo(28, 4);
    ctx.lineTo(1, 3);
    ctx.closePath();
    ctx.fill();
    ctx.stroke();

    ctx.strokeStyle = isSlamming ? "#ff2200" : "#8f2222";
    ctx.lineWidth = 1.2;
    ctx.beginPath();
    ctx.moveTo(4, 0);
    ctx.lineTo(22, 0);
    ctx.stroke();
  }

  ctx.restore();
  ctx.restore();
  ctx.restore();
}

function drawEnemy(e, angle) {
  ctx.save();
  ctx.translate(e.x, e.y);
  ctx.rotate(angle);

  const isCaster = ["pyromancer", "cryomancer", "electromancer"].includes(e.type);

  if (isCaster) {
    let robeColor = "#7f1d1d";
    let auraColor = "#ef4444";
    if (e.type === "cryomancer") { robeColor = "#1e3a8a"; auraColor = "#60a5fa"; }
    if (e.type === "electromancer") { robeColor = "#581c87"; auraColor = "#fde047"; }

    ctx.fillStyle = robeColor;
    ctx.strokeStyle = "#000";
    ctx.lineWidth = 1.5;
    ctx.beginPath();
    ctx.moveTo(-10, -12);
    ctx.lineTo(-24, 0);
    ctx.lineTo(-10, 12);
    ctx.closePath();
    ctx.fill();
    ctx.stroke();

    ctx.beginPath();
    ctx.arc(0, 0, 11, 0, Math.PI * 2);
    ctx.fill();
    ctx.stroke();

    ctx.fillStyle = auraColor;
    ctx.beginPath();
    ctx.arc(6, -3, 2, 0, Math.PI * 2);
    ctx.arc(6, 3, 2, 0, Math.PI * 2);
    ctx.fill();

    ctx.fillStyle = auraColor;
    ctx.beginPath();
    ctx.arc(14, 8, 4.5, 0, Math.PI * 2);
    ctx.fill();
    ctx.strokeStyle = "#fff";
    ctx.lineWidth = 1;
    ctx.stroke();

  } else if (e.type === "skeleton") {
    const stride = Math.sin(e.walkTimer) * 5.5;
    ctx.strokeStyle = "#1a1814";
    ctx.fillStyle = "#dbd5c5";
    ctx.lineWidth = 1.5;
    ctx.fillRect(-6 + stride, -12, 7, 4);
    ctx.strokeRect(-6 + stride, -12, 7, 4);
    ctx.fillRect(-6 - stride, 8, 7, 4);
    ctx.strokeRect(-6 - stride, 8, 7, 4);

    ctx.fillStyle = "#ccc5b2";
    ctx.beginPath();
    ctx.ellipse(-1, 0, 9, 8, 0, 0, Math.PI * 2);
    ctx.fill();
    ctx.stroke();

    ctx.fillStyle = "#eee6d3";
    ctx.beginPath();
    ctx.arc(2, 0, 7, 0, Math.PI * 2);
    ctx.fill();
    ctx.stroke();

    ctx.fillStyle = "#0d0c0a";
    ctx.beginPath();
    ctx.arc(5, -2.5, 1.8, 0, Math.PI * 2);
    ctx.arc(5, 2.5, 1.8, 0, Math.PI * 2);
    ctx.fill();

    ctx.save();
    const attackSwing = e.attackCooldown > 0 ? Math.sin(e.attackCooldown * 0.3) * 0.8 : 0.2;
    ctx.translate(10, 8);
    ctx.rotate(attackSwing);
    ctx.fillStyle = "#705f4d";
    ctx.beginPath();
    ctx.moveTo(0, -2);
    ctx.lineTo(20, -3);
    ctx.lineTo(24, 0);
    ctx.lineTo(20, 3);
    ctx.lineTo(0, 2);
    ctx.closePath();
    ctx.fill();
    ctx.restore();

  } else {
    const phase = e.walkTimer;
    const leftStride = Math.sin(phase) * 6.5;
    const leftLift = Math.max(0, -Math.cos(phase)) * 2.5;
    const rightStride = Math.sin(phase - 1.1) * 7.5 - 2;

    ctx.save();
    ctx.translate(-3, -10);
    ctx.fillStyle = "#222921";
    ctx.fillRect(-4, -4, 8, 5);
    ctx.fillStyle = "#3e523b";
    ctx.beginPath();
    ctx.ellipse(3 + leftStride, -1 - leftLift, 5, 2.5, 0, 0, Math.PI * 2);
    ctx.fill();
    ctx.restore();

    ctx.save();
    ctx.translate(-4, 9);
    ctx.fillStyle = "#1e241d";
    ctx.fillRect(-5, 0, 7, 5);
    ctx.fillStyle = "#3b4d38";
    ctx.beginPath();
    ctx.ellipse(2 + rightStride, 2, 6, 2.5, -0.3, 0, Math.PI * 2);
    ctx.fill();
    ctx.restore();

    ctx.fillStyle = "#495c47";
    ctx.strokeStyle = "#182117";
    ctx.lineWidth = 1.5;
    ctx.beginPath();
    ctx.ellipse(0, 0, 13, 11, 0, 0, Math.PI * 2);
    ctx.fill();
    ctx.stroke();

    ctx.fillStyle = "#5c7359";
    ctx.beginPath();
    ctx.arc(4, 0, 8, 0, Math.PI * 2);
    ctx.fill();
    ctx.stroke();

    ctx.fillStyle = "#e8f0e6";
    ctx.beginPath();
    ctx.arc(8, -2.5, 2, 0, Math.PI * 2);
    ctx.fill();
    ctx.fillStyle = "#111";
    ctx.fillRect(8.5, -3, 1, 1);

    const lunge = e.attackCooldown > 0 ? 10 : 0;
    ctx.fillStyle = "#445941";
    ctx.fillRect(8 + lunge, -9, 12, 4);
    ctx.fillRect(8 + lunge, 5, 12, 4);
  }

  ctx.restore();
}

function drawBoss(b) {
  ctx.save();
  ctx.translate(b.x, b.y);
  ctx.rotate(b.angle);

  ctx.fillStyle = "#15091e";
  ctx.strokeStyle = "#08040d";
  ctx.lineWidth = 2;
  ctx.beginPath();
  ctx.moveTo(-12, -18);
  ctx.lineTo(-32, 0);
  ctx.lineTo(-12, 18);
  ctx.closePath();
  ctx.fill();
  ctx.stroke();

  ctx.fillStyle = "#251233";
  ctx.beginPath();
  ctx.ellipse(0, 0, 18, 14, 0, 0, Math.PI * 2);
  ctx.fill();
  ctx.stroke();

  ctx.fillStyle = "#ded3be";
  ctx.beginPath();
  ctx.arc(4, 0, 11, 0, Math.PI * 2);
  ctx.fill();
  ctx.stroke();

  ctx.fillStyle = "#3b2b42";
  ctx.beginPath();
  ctx.moveTo(4, -10);
  ctx.lineTo(-6, -20);
  ctx.lineTo(0, -9);
  ctx.moveTo(4, 10);
  ctx.lineTo(-6, 20);
  ctx.lineTo(0, 9);
  ctx.fill();

  ctx.fillStyle = "#e040fb";
  ctx.beginPath();
  ctx.arc(9, -4, 2.5, 0, Math.PI * 2);
  ctx.arc(9, 4, 2.5, 0, Math.PI * 2);
  ctx.fill();

  ctx.save();
  ctx.translate(14, 14);
  ctx.fillStyle = "#110b14";
  ctx.fillRect(-2, -2, 4, 34);
  ctx.fillStyle = "#e040fb";
  ctx.beginPath();
  ctx.arc(0, -6, 7, 0, Math.PI * 2);
  ctx.fill();
  ctx.restore();

  if (b.boneShield) {
    const time = Date.now() * 0.005;
    ctx.strokeStyle = "rgba(56, 189, 248, 0.75)";
    ctx.lineWidth = 3;
    ctx.beginPath();
    ctx.arc(0, 0, 46, 0, Math.PI * 2);
    ctx.stroke();

    for (let i = 0; i < 5; i++) {
      const bAng = time + (i / 5) * Math.PI * 2;
      const bx = Math.cos(bAng) * 46;
      const by = Math.sin(bAng) * 46;
      ctx.fillStyle = "#fff8e7";
      ctx.fillRect(bx - 3, by - 6, 6, 12);
    }
  }

  ctx.restore();
}

// --- MAIN LOOP ---
function update() {
  if (isPaused || isTreeOpen) return;

  mouseWorldX = hero.x + (mouseScreenX - width / 2) / CAMERA_ZOOM;
  mouseWorldY = hero.y + (mouseScreenY - height / 2) / CAMERA_ZOOM;

  if (hero.hp <= 0 && !hero.isDead) {
    hero.isDead = true;
    hero.hp = 0;
    playSound('death');
    document.getElementById('death-screen').style.display = 'flex';
  }

  if (!hero.isDead && hero.mana < hero.maxMana) {
    hero.mana = Math.min(hero.maxMana, hero.mana + hero.manaRegen);
    renderD2Globes();
  }

  if (hero.hasRage && hero.rage > 0) {
    hero.rageDecayTimer++;
    if (hero.rageDecayTimer >= 60) {
      hero.rage = Math.max(0, hero.rage - 1);
      hero.rageDecayTimer = 0;
      renderD2Globes();
    }
  }

  if (!hero.isDead) {
    let mx = 0, my = 0;
    if (keys['w']) my -= 1;
    if (keys['s']) my += 1;
    if (keys['a']) mx -= 1;
    if (keys['d']) mx += 1;

    const isMoving = mx !== 0 || my !== 0;
    if (isMoving) {
      const len = Math.hypot(mx, my);
      const dx = (mx / len) * hero.speed;
      const dy = (my / len) * hero.speed;
      hero.x += dx;
      hero.y += dy;
      hero.walkTimer += Math.hypot(dx, dy) * 0.22;
    } else {
      hero.idleTimer += 0.04;
    }
    hero.x = Math.max(hero.radius, Math.min(WORLD_WIDTH - hero.radius, hero.x));
    hero.y = Math.max(hero.radius, Math.min(WORLD_HEIGHT - hero.radius, hero.y));
    resolveCollisions(hero);

    if (hero.swingCooldown > 0) hero.swingCooldown--;
    if (hero.slamTimer > 0) hero.slamTimer--;
    if (hero.shootingSkyTimer > 0) hero.shootingSkyTimer--;
  }

  // Ground Cracks
  for (let i = groundEffects.length - 1; i >= 0; i--) {
    const ge = groundEffects[i];
    ge.life--;
    if (ge.life <= 0) groundEffects.splice(i, 1);
  }

  // Boss AI
  if (boss.active) {
    boss.walkTimer += 0.05;
    const bDx = hero.x - boss.x;
    const bDy = hero.y - boss.y;
    const bDist = Math.hypot(bDx, bDy);
    boss.angle = Math.atan2(bDy, bDx);

    const bossBar = document.getElementById('boss-bar-container');
    if (bDist < 850) {
      bossBar.style.display = 'flex';
      document.getElementById('boss-bar-fill').style.width = `${(boss.hp / boss.maxHp) * 100}%`;
    } else {
      bossBar.style.display = 'none';
    }

    if (boss.boneShield) {
      boss.boneShieldTimer--;
      if (boss.boneShieldTimer <= 0) {
        boss.boneShield = false;
        boss.boneShieldCooldown = 1800;
      }
    } else {
      if (boss.boneShieldCooldown > 0) boss.boneShieldCooldown--;
      else if (bDist < 700) {
        boss.boneShield = true;
        boss.boneShieldTimer = 300;
        playSound('deflect');
        createFloatingText(boss.x, boss.y - 35, "BONE SHIELD!", "#38bdf8");
      }
    }

    if (boss.chaosBoltCooldown > 0) boss.chaosBoltCooldown--;
    else if (bDist < 750) {
      boss.chaosBoltCooldown = 180;
      playSound('toxic');
      for (let i = -1; i <= 1; i++) {
        const cAng = boss.angle + i * 0.3;
        projectiles.push({
          type: "chaos_bolt",
          x: boss.x, y: boss.y,
          vx: Math.cos(cAng) * 3.5,
          vy: Math.sin(cAng) * 3.5,
          radius: 12,
          life: 140
        });
      }
    }

    if (boss.summonCooldown > 0) boss.summonCooldown--;
    else if (bDist < 800) {
      boss.summonCooldown = 550;
      playSound('summon');
      createFloatingText(boss.x, boss.y - 35, "RISE!", "#902090");
      for (let i = 0; i < 2; i++) {
        enemies.push({
          type: "skeleton",
          x: boss.x + (Math.random() * 80 - 40),
          y: boss.y + (Math.random() * 80 - 40),
          radius: 15,
          speed: 1.4,
          hp: 80,
          maxHp: 80,
          fireRes: 0.10,
          coldRes: 0.10,
          lightningRes: 0.10,
          burnTimer: 0,
          burnDps: 0,
          poisonTimer: 0,
          poisonDps: 0,
          attackCooldown: 0,
          walkTimer: 0
        });
      }
      enemies.push({
        type: "zombie",
        x: boss.x + (Math.random() * 80 - 40),
        y: boss.y + (Math.random() * 80 - 40),
        radius: 18,
        speed: 1.0,
        hp: 180,
        maxHp: 180,
        fireRes: 0.0,
        coldRes: 0.0,
        lightningRes: 0.0,
        burnTimer: 0,
        burnDps: 0,
        poisonTimer: 0,
        poisonDps: 0,
        attackCooldown: 0,
        walkTimer: 0
      });
    }

    if (bDist > 160 && bDist < 800) {
      boss.x += (bDx / bDist) * boss.speed;
      boss.y += (bDy / bDist) * boss.speed;
      resolveCollisions(boss);
    }
  }

  // Burning & Poisoning DoT Ticks on Enemies & Boss
  const dotTargets = [...enemies, ...(boss.active ? [boss] : [])];
  dotTargets.forEach(e => {
    // 1. Burning DoT
    if (e.burnTimer && e.burnTimer > 0) {
      e.burnTimer--;
      const fireRes = e.fireRes || 0.0;
      const tickDmg = (e.burnDps / 60) * (1 - fireRes);
      e.hp -= tickDmg;

      if (Math.random() < 0.22) {
        particles.push({
          x: e.x + (Math.random() * 18 - 9),
          y: e.y + (Math.random() * 18 - 9),
          vx: (Math.random() - 0.5) * 1.5,
          vy: -1.6 - Math.random() * 1.6,
          radius: 2.2,
          color: "#ff6a00",
          life: 15
        });
      }

      if (e.hp <= 0) {
        if (e === boss) killBoss();
        else killEnemy(e, enemies.indexOf(e));
      }
    } else {
      e.burnDps = 0;
    }

    // 2. Toxic Rain Stacking Poison DoT
    if (e.poisonTimer && e.poisonTimer > 0) {
      e.poisonTimer--;
      const tickDmg = (e.poisonDps / 60);
      e.hp -= tickDmg;

      if (Math.random() < 0.22) {
        particles.push({
          x: e.x + (Math.random() * 18 - 9),
          y: e.y + (Math.random() * 18 - 9),
          vx: (Math.random() - 0.5) * 1.4,
          vy: -1.4 - Math.random() * 1.4,
          radius: 2.2,
          color: "#22c55e",
          life: 15
        });
      }

      if (e.hp <= 0) {
        if (e === boss) killBoss();
        else killEnemy(e, enemies.indexOf(e));
      }
    } else {
      e.poisonDps = 0;
    }
  });

  // Minions
  playerMinions.forEach(m => {
    let nearest = null;
    let nearestDist = 450;
    const targets = [...enemies, ...(boss.active ? [boss] : [])];
    targets.forEach(e => {
      const d = Math.hypot(e.x - m.x, e.y - m.y);
      if (d < nearestDist) { nearestDist = d; nearest = e; }
    });

    if (nearest) {
      const angle = Math.atan2(nearest.y - m.y, nearest.x - m.x);
      m.x += Math.cos(angle) * m.speed;
      m.y += Math.sin(angle) * m.speed;
      m.walkTimer += 0.12;
      if (nearestDist < m.radius + nearest.radius + 6) {
        if (m.attackCooldown <= 0) {
          m.attackCooldown = 30;
          damageTarget(nearest, { phys: 75, fire: 0, cold: 0, lightning: 0 });
        }
      }
    } else {
      const d = Math.hypot(hero.x - m.x, hero.y - m.y);
      if (d > 60) {
        const angle = Math.atan2(hero.y - m.y, hero.x - m.x);
        m.x += Math.cos(angle) * m.speed;
        m.y += Math.sin(angle) * m.speed;
        m.walkTimer += 0.12;
      }
    }
    if (m.attackCooldown > 0) m.attackCooldown--;
  });

  // Regular Enemies AI
  enemies.forEach(e => {
    const dx = hero.x - e.x;
    const dy = hero.y - e.y;
    const dist = Math.hypot(dx, dy);
    const isCaster = ["pyromancer", "cryomancer", "electromancer"].includes(e.type);
    const dMult = e.dmgMult || 1.0;

    if (isCaster) {
      if (dist < 340) {
        if (e.attackCooldown <= 0 && !hero.isDead) {
          e.attackCooldown = 85;
          const castAngle = Math.atan2(dy, dx);

          if (e.type === "pyromancer") {
            projectiles.push({
              type: "enemy_fire_orb",
              x: e.x, y: e.y,
              vx: Math.cos(castAngle) * 4.2,
              vy: Math.sin(castAngle) * 4.2,
              radius: 9,
              damage: { phys: 0, fire: Math.round(65 * dMult), cold: 0, lightning: 0 },
              life: 90
            });
          } else if (e.type === "cryomancer") {
            projectiles.push({
              type: "enemy_frost_shard",
              x: e.x, y: e.y,
              vx: Math.cos(castAngle) * 5.2,
              vy: Math.sin(castAngle) * 5.2,
              radius: 8,
              damage: { phys: 0, fire: 0, cold: Math.round(60 * dMult), lightning: 0 },
              life: 90
            });
          } else if (e.type === "electromancer") {
            projectiles.push({
              type: "enemy_lightning_chain",
              x: e.x, y: e.y,
              vx: Math.cos(castAngle) * 6.5,
              vy: Math.sin(castAngle) * 6.5,
              radius: 8,
              damage: { phys: 0, fire: 0, cold: 0, lightning: Math.round((Math.floor(Math.random() * 60) + 30) * dMult) },
              life: 80
            });
          }
        }
      } else {
        const moveX = (dx / dist) * e.speed;
        const moveY = (dy / dist) * e.speed;
        e.x += moveX;
        e.y += moveY;
        e.walkTimer += Math.hypot(moveX, moveY) * 0.185;
        resolveCollisions(e);
      }
    } else {
      if (dist < hero.radius + e.radius + 8) {
        if (e.attackCooldown <= 0 && !hero.isDead) {
          e.attackCooldown = 40;
          damageHero({ phys: Math.round((e.type === "skeleton" ? 35 : 55) * dMult), fire: 0, cold: 0, lightning: 0 });
          if (e.type === "skeleton") playSound('clack');
        }
      } else {
        const moveX = (dx / dist) * e.speed;
        const moveY = (dy / dist) * e.speed;
        e.x += moveX;
        e.y += moveY;
        e.walkTimer += Math.hypot(moveX, moveY) * 0.185;
        resolveCollisions(e);
      }
    }

    if (e.attackCooldown > 0) e.attackCooldown--;
  });

  // Projectiles
  for (let i = projectiles.length - 1; i >= 0; i--) {
    const p = projectiles[i];

    if (p.type === "sky_arrow") {
      p.x += p.vx;
      p.y += p.vy;
      p.life--;
      if (Math.random() < 0.6) {
        particles.push({
          x: p.x, y: p.y,
          vx: (Math.random() - 0.5) * 1.5, vy: 2,
          radius: 2.2, color: "#4ade80", life: 10
        });
      }
      if (p.life <= 0) projectiles.splice(i, 1);
    } 
    else if (p.type === "toxic_rain_arrow") {
      if (p.delay > 0) {
        p.delay--;
        continue;
      }
      p.x += p.vx;
      p.y += p.vy;
      p.life--;

      if (p.y >= p.destY || p.life <= 0) {
        const targets = [...enemies, ...(boss.active ? [boss] : [])];
        const dotMult = hero.dotMultiplier || 0.0;
        // Formula: 25 * (1 + DoT Multiplier). 80% becomes 45 DPS per hit!
        const addedPoisonDps = Math.round(25 * (1 + dotMult));

        targets.forEach(e => {
          if (Math.hypot(e.x - p.destX, e.y - p.destY) < 24 + e.radius) {
            // ZERO INITIAL INSTANT DAMAGE, PURE STACKING POISON DOT!
            e.poisonDps = (e.poisonDps || 0) + addedPoisonDps;
            e.poisonTimer = 240; // 4 seconds duration
            createFloatingText(e.x, e.y - 20, `+${addedPoisonDps} POISON/s`, "#22c55e");
          }
        });

        const incAoe = chestSockets.some(s => s && s.id === "sup_11");
        projectiles.push({
          type: "toxic_pod",
          x: p.destX,
          y: p.destY,
          radius: incAoe ? 24 : 17,
          addedDps: addedPoisonDps,
          life: 85
        });

        for (let k = 0; k < 3; k++) {
          particles.push({
            x: p.destX, y: p.destY,
            vx: (Math.random() - 0.5) * 4,
            vy: (Math.random() - 0.5) * 4,
            radius: 2,
            color: "#4ade80",
            life: 14
          });
        }
        projectiles.splice(i, 1);
      }
    }
    else if (p.type === "hero_arrow") {
      p.x += p.vx;
      p.y += p.vy;
      p.life--;
      const targets = [...enemies, ...(boss.active ? [boss] : [])];
      for (let t of targets) {
        if (Math.hypot(t.x - p.x, t.y - p.y) < p.radius + t.radius) {
          damageTarget(t, p.damageObj);
          p.life = 0;
          break;
        }
      }
      if (p.life <= 0) projectiles.splice(i, 1);
    } 
    else if (p.type === "molten_ball") {
      p.x += p.vx;
      p.y += p.vy;
      p.life--;
      p.bounceTimer--;

      if (Math.random() < 0.45) {
        particles.push({
          x: p.x, y: p.y,
          vx: (Math.random() - 0.5) * 2,
          vy: (Math.random() - 0.5) * 2,
          radius: 2.2,
          color: "#ff8c00",
          life: 12
        });
      }

      // Projectile bounce mechanics (slower vs accelerated with Faster Proj)
      if (p.bounceTimer <= 0 && p.bounces > 0) {
        p.bounces--;
        p.bounceTimer = p.fasterProj ? 14 : 26;
        p.vx *= (p.fasterProj ? 0.65 : 0.78);
        p.vy *= (p.fasterProj ? 0.65 : 0.78);
        createSplatter(p.x, p.y, "#ff5500", 5);
      }

      if (p.life <= 0) {
        const targets = [...enemies, ...(boss.active ? [boss] : [])];
        targets.forEach(e => {
          if (Math.hypot(e.x - p.x, e.y - p.y) < p.radius + e.radius) {
            const fireRes = e.fireRes || 0.0;
            const baseFire = 140 + hero.flatFire + (p.spellDmg || 0);
            const isCrit = Math.random() < hero.critChance;
            const instantFireDmg = Math.round(baseFire * (1 - fireRes) * (isCrit ? hero.critMult : 1.0));

            if (e === boss && boss.boneShield) {
              playSound('deflect');
              createFloatingText(boss.x, boss.y - 30, "INVULNERABLE!", "#38bdf8");
            } else {
              e.hp -= instantFireDmg;
              // Stacking Burning DoT increases DPS by 10 per hit!
              e.burnDps = (e.burnDps || 0) + 10;
              e.burnTimer = 240;
              createFloatingText(e.x, e.y - 20, `+10 BURN/s (${e.burnDps})`, "#ff8c00");
              createSplatter(e.x, e.y, "#ff5500", 9);

              if (e.hp <= 0) {
                if (e === boss) killBoss();
                else killEnemy(e, enemies.indexOf(e));
              }
            }
          }
        });
        projectiles.splice(i, 1);
      }
    } 
    else if (p.type === "toxic_pod") {
      p.life--;
      const targets = [...enemies, ...(boss.active ? [boss] : [])];
      targets.forEach(e => {
        if (Math.hypot(e.x - p.x, e.y - p.y) < p.radius + e.radius) {
          e.poisonDps = (e.poisonDps || 0) + (1.2 * (1 + (hero.dotMultiplier || 0)));
          e.poisonTimer = 180;
        }
      });
      if (p.life <= 0) projectiles.splice(i, 1);
    } 
    else if (p.type === "enemy_fire_orb" || p.type === "enemy_frost_shard" || p.type === "enemy_lightning_chain") {
      p.x += p.vx;
      p.y += p.vy;
      p.life--;

      if (Math.hypot(hero.x - p.x, hero.y - p.y) < hero.radius + p.radius && !hero.isDead) {
        damageHero(p.damage);
        projectiles.splice(i, 1);
      } else if (p.life <= 0) {
        projectiles.splice(i, 1);
      }
    }
    else if (p.type === "chaos_bolt") {
      p.x += p.vx;
      p.y += p.vy;
      p.life--;
      const hDx = hero.x - p.x;
      const hDy = hero.y - p.y;
      const hDist = Math.hypot(hDx, hDy);
      if (hDist < 20 && !hero.isDead) {
        const bMult = boss.dmgMult || 1.0;
        damageHero({
          phys: Math.round(20 * bMult),
          fire: Math.round(15 * bMult),
          cold: Math.round(15 * bMult),
          lightning: Math.round(15 * bMult)
        });
        createSplatter(hero.x, hero.y, "#902090", 12);
        projectiles.splice(i, 1);
      } else if (p.life <= 0) {
        projectiles.splice(i, 1);
      }
    }
  }

  // Auto Pickup
  for (let i = drops.length - 1; i >= 0; i--) {
    const d = drops[i];
    if (Math.hypot(hero.x - d.x, hero.y - d.y) < hero.radius + 20) {
      if (d.type === "organ" || d.type === "heart") {
        const freeIdx = stashOrgans.indexOf(null);
        if (freeIdx !== -1) {
          stashOrgans[freeIdx] = { id: Math.random(), type: d.organType, name: d.name };
          drops.splice(i, 1);
          renderUI();
        }
      } else {
        const freeIdx = stashGear.indexOf(null);
        if (freeIdx !== -1) {
          stashGear[freeIdx] = {
            id: Math.random(),
            slot: d.slot,
            base: d.base,
            name: d.name,
            baseArmour: d.baseArmour,
            rarity: d.rarity,
            prefixes: [],
            suffixes: []
          };
          drops.splice(i, 1);
          renderUI();
        }
      }
    }
  }

  for (let i = particles.length - 1; i >= 0; i--) {
    const p = particles[i];
    p.x += p.vx;
    p.y += p.vy;
    p.life--;
    if (p.life <= 0) particles.splice(i, 1);
  }
}

function draw() {
  ctx.clearRect(0, 0, width, height);

  ctx.save();
  ctx.translate(width / 2, height / 2);
  ctx.scale(CAMERA_ZOOM, CAMERA_ZOOM);
  ctx.translate(-hero.x, -hero.y);

  // 1. Dark Jungle Grass Floor
  ctx.fillStyle = "#020703";
  ctx.fillRect(0, 0, WORLD_WIDTH, WORLD_HEIGHT);

  // 2. Cobblestone Roads
  worldRoads.forEach(r => {
    ctx.strokeStyle = "#161f16";
    ctx.lineWidth = r.width;
    ctx.lineCap = "round";
    ctx.beginPath();
    ctx.moveTo(r.x1, r.y1);
    ctx.lineTo(r.x2, r.y2);
    ctx.stroke();

    ctx.strokeStyle = "#242e23";
    ctx.lineWidth = r.width * 0.6;
    ctx.beginPath();
    ctx.moveTo(r.x1, r.y1);
    ctx.lineTo(r.x2, r.y2);
    ctx.stroke();
  });

  // 3. Ground Cracks from Molten Strike
  groundEffects.forEach(ge => {
    if (ge.type === "ground_crack") {
      const alpha = ge.life / ge.maxLife;
      ctx.save();
      ctx.translate(ge.x, ge.y);
      ctx.strokeStyle = `rgba(255, 90, 20, ${alpha})`;
      ctx.lineWidth = 3.6;
      ctx.shadowColor = "#ff4500";
      ctx.shadowBlur = 12 * alpha;
      ctx.beginPath();
      ge.lines.forEach(l => {
        ctx.moveTo(0, 0);
        ctx.lineTo(l.dx, l.dy);
      });
      ctx.stroke();

      ctx.strokeStyle = `rgba(255, 230, 90, ${alpha})`;
      ctx.lineWidth = 1.6;
      ctx.stroke();
      ctx.restore();
    }
  });

  // Boss Sanctuary Tiles
  ctx.fillStyle = "#120a17";
  ctx.beginPath();
  ctx.arc(2250, 2250, 420, 0, Math.PI * 2);
  ctx.fill();
  ctx.strokeStyle = "#431c59";
  ctx.lineWidth = 4;
  ctx.stroke();

  // World Boundary
  ctx.strokeStyle = "#2f4530";
  ctx.lineWidth = 8;
  ctx.strokeRect(0, 0, WORLD_WIDTH, WORLD_HEIGHT);

  // Props
  obstacles.forEach(obs => {
    if (obs.type === "house") {
      ctx.fillStyle = "#1c241c";
      ctx.strokeStyle = "#0e140e";
      ctx.lineWidth = 3;
      ctx.fillRect(obs.x, obs.y, obs.w, obs.h);
      ctx.strokeRect(obs.x, obs.y, obs.w, obs.h);
      ctx.fillStyle = "#060907";
      ctx.fillRect(obs.x + obs.w/2 - 12, obs.y + obs.h - 20, 24, 20);
    } else if (obs.type === "tree") {
      ctx.fillStyle = "#0d2611";
      ctx.beginPath();
      ctx.arc(obs.x, obs.y, obs.canopyR, 0, Math.PI * 2);
      ctx.fill();
      ctx.fillStyle = "#2e1e12";
      ctx.beginPath();
      ctx.arc(obs.x, obs.y, obs.radius, 0, Math.PI * 2);
      ctx.fill();
    } else if (obs.type === "rock") {
      ctx.fillStyle = "#333833";
      ctx.beginPath();
      ctx.arc(obs.x, obs.y, obs.radius, 0, Math.PI * 2);
      ctx.fill();
    } else if (obs.type === "pillar") {
      ctx.fillStyle = "#3a2c42";
      ctx.strokeStyle = "#634773";
      ctx.lineWidth = 2;
      ctx.beginPath();
      ctx.arc(obs.x, obs.y, obs.radius, 0, Math.PI * 2);
      ctx.fill();
      ctx.stroke();
    }
  });

  // Drops
  drops.forEach(d => {
    ctx.fillStyle = d.color;
    ctx.beginPath();
    ctx.arc(d.x, d.y, 5, 0, Math.PI * 2);
    ctx.fill();

    ctx.font = "bold 10px monospace";
    ctx.fillStyle = "rgba(0,0,0,0.85)";
    const tw = ctx.measureText(d.name).width;
    ctx.fillRect(d.x - tw/2 - 4, d.y - 18, tw + 8, 14);
    ctx.strokeStyle = d.color;
    ctx.lineWidth = 1;
    ctx.strokeRect(d.x - tw/2 - 4, d.y - 18, tw + 8, 14);
    ctx.fillStyle = d.color;
    ctx.fillText(d.name, d.x - tw/2, d.y - 7);
  });

  // Projectiles
  projectiles.forEach(p => {
    if (p.type === "hero_arrow") {
      ctx.strokeStyle = "#ffffff";
      ctx.lineWidth = 2;
      ctx.beginPath();
      ctx.moveTo(p.x, p.y);
      ctx.lineTo(p.x + p.vx * 2, p.y + p.vy * 2);
      ctx.stroke();
    } 
    else if (p.type === "sky_arrow") {
      ctx.strokeStyle = "#4ade80";
      ctx.lineWidth = 2.5;
      ctx.beginPath();
      ctx.moveTo(p.x, p.y);
      ctx.lineTo(p.x, p.y + 18);
      ctx.stroke();
      ctx.fillStyle = "#a7f3d0";
      ctx.beginPath();
      ctx.arc(p.x, p.y, 3, 0, Math.PI * 2);
      ctx.fill();
    }
    else if (p.type === "toxic_rain_arrow") {
      if (p.delay <= 0) {
        ctx.strokeStyle = "#4ade80";
        ctx.lineWidth = 2;
        ctx.beginPath();
        ctx.moveTo(p.x, p.y);
        ctx.lineTo(p.x - p.vx * 1.5, p.y - p.vy * 1.5);
        ctx.stroke();

        ctx.fillStyle = "#86efac";
        ctx.beginPath();
        ctx.arc(p.x, p.y, 2.5, 0, Math.PI * 2);
        ctx.fill();
      }
    }
    else if (p.type === "molten_ball") {
      ctx.fillStyle = "#ff5500";
      ctx.beginPath(); ctx.arc(p.x, p.y, 7.5, 0, Math.PI * 2); ctx.fill();
      ctx.fillStyle = "#ffea00";
      ctx.beginPath(); ctx.arc(p.x, p.y, 4, 0, Math.PI * 2); ctx.fill();
      ctx.strokeStyle = "#ff2200";
      ctx.lineWidth = 1.5;
      ctx.stroke();
    } 
    else if (p.type === "toxic_pod") {
      ctx.fillStyle = "rgba(40, 180, 70, 0.35)";
      ctx.beginPath(); ctx.arc(p.x, p.y, p.radius, 0, Math.PI * 2);
      ctx.fill();
      ctx.strokeStyle = "#86efac";
      ctx.stroke();
    } 
    else if (p.type === "enemy_fire_orb") {
      ctx.fillStyle = "#ef4444";
      ctx.beginPath(); ctx.arc(p.x, p.y, 8, 0, Math.PI * 2); ctx.fill();
      ctx.fillStyle = "#fbbf24";
      ctx.beginPath(); ctx.arc(p.x, p.y, 4.5, 0, Math.PI * 2); ctx.fill();
    }
    else if (p.type === "enemy_frost_shard") {
      ctx.fillStyle = "#38bdf8";
      ctx.beginPath(); ctx.arc(p.x, p.y, 7, 0, Math.PI * 2); ctx.fill();
      ctx.fillStyle = "#ffffff";
      ctx.beginPath(); ctx.arc(p.x, p.y, 3.5, 0, Math.PI * 2); ctx.fill();
    }
    else if (p.type === "enemy_lightning_chain") {
      ctx.fillStyle = "#fde047";
      ctx.beginPath(); ctx.arc(p.x, p.y, 7.5, 0, Math.PI * 2); ctx.fill();
      ctx.strokeStyle = "#fff";
      ctx.lineWidth = 1.5;
      ctx.stroke();
    }
    else if (p.type === "chaos_bolt") {
      ctx.fillStyle = "#e040fb";
      ctx.beginPath(); ctx.arc(p.x, p.y, 8, 0, Math.PI * 2); ctx.fill();
      ctx.fillStyle = "#ffffff";
      ctx.beginPath(); ctx.arc(p.x, p.y, 4, 0, Math.PI * 2); ctx.fill();
    }
  });

  // Minions
  playerMinions.forEach(m => {
    ctx.save();
    ctx.translate(m.x, m.y);
    ctx.fillStyle = "#3e5c3e";
    ctx.beginPath(); ctx.arc(0, 0, m.radius, 0, Math.PI * 2); ctx.fill();
    ctx.strokeStyle = "#4ade80"; ctx.lineWidth = 2; ctx.stroke();
    ctx.restore();
  });

  // Enemies
  enemies.forEach(e => {
    const angle = Math.atan2(hero.y - e.y, hero.x - e.x);
    drawEnemy(e, angle);
  });

  // Boss
  if (boss.active) drawBoss(boss);

  // Hero
  if (!hero.isDead) {
    const heroAngle = Math.atan2(mouseWorldY - hero.y, mouseWorldX - hero.x);
    const isMoving = keys['w'] || keys['s'] || keys['a'] || keys['d'];
    drawHero(hero.x, hero.y, heroAngle, isMoving, hero.walkTimer, hero.idleTimer, hero.swingCooldown);

    if (hero.swingCooldown > 8 && getEquippedWeaponType() !== "bow" && hero.slamTimer <= 0) {
      ctx.save();
      ctx.translate(hero.x, hero.y);
      ctx.rotate(hero.swingAngle);
      ctx.beginPath();
      ctx.arc(0, 0, 80, -Math.PI * 0.4, Math.PI * 0.4);
      ctx.strokeStyle = "rgba(180, 255, 200, 0.75)";
      ctx.lineWidth = 7;
      ctx.stroke();
      ctx.restore();
    }
  }

  // Particles
  particles.forEach(p => {
    if (p.text) {
      ctx.font = "bold 12px monospace";
      ctx.fillStyle = p.color;
      ctx.fillText(p.text, p.x - ctx.measureText(p.text).width / 2, p.y);
    } else {
      ctx.fillStyle = p.color;
      ctx.beginPath();
      ctx.arc(p.x, p.y, p.radius, 0, Math.PI * 2);
      ctx.fill();
    }
  });

  // Torchlight
  const lantern = ctx.createRadialGradient(hero.x, hero.y, 45, hero.x, hero.y, 380);
  lantern.addColorStop(0, "rgba(0,0,0,0)");
  lantern.addColorStop(0.65, "rgba(2,6,2,0.65)");
  lantern.addColorStop(1, "rgba(1,3,1,0.98)");
  ctx.fillStyle = lantern;
  ctx.fillRect(hero.x - width, hero.y - height, width * 2, height * 2);

  ctx.restore();

  requestAnimationFrame(() => {
    update();
    draw();
  });
}

recalculateStats();
renderUI();
startWave(1);
draw();
</script>
</body>
</html>
