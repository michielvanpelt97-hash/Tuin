<!DOCTYPE html>
<html lang="nl">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Tuinontwerp App</title>
  <style>
    *, *::before, *::after { box-sizing: border-box; margin: 0; padding: 0; }
    body { font-family: system-ui, -apple-system, sans-serif; background: #f0f4f0; color: #222; height: 100vh; display: flex; flex-direction: column; overflow: hidden; }

    header { background: #2e7d32; color: white; padding: 10px 16px; display: flex; align-items: center; gap: 12px; }
    header h1 { font-size: 18px; font-weight: 600; }
    header span { font-size: 13px; opacity: 0.8; }
    .header-actions { margin-left: auto; display: flex; gap: 8px; }
    .header-btn { background: rgba(255,255,255,0.2); border: 1px solid rgba(255,255,255,0.4); color: white; padding: 5px 12px; border-radius: 6px; cursor: pointer; font-size: 12px; }
    .header-btn:hover { background: rgba(255,255,255,0.3); }

    #main { display: flex; flex: 1; overflow: hidden; }

    /* Sidebar */
    #sidebar { width: 210px; min-width: 210px; background: white; border-right: 1px solid #ddd; display: flex; flex-direction: column; }
    #sidebar-header { padding: 10px 12px 6px; border-bottom: 1px solid #eee; }
    #sidebar-header h2 { font-size: 12px; font-weight: 600; color: #666; text-transform: uppercase; letter-spacing: 0.05em; margin-bottom: 8px; }
    .tab-row { display: flex; gap: 4px; }
    .tab { flex: 1; font-size: 11px; padding: 5px 2px; border: 1px solid #ddd; border-radius: 6px; background: transparent; cursor: pointer; color: #555; }
    .tab.active { background: #e8f5e9; color: #2e7d32; border-color: #a5d6a7; font-weight: 600; }
    #item-list { flex: 1; overflow-y: auto; padding: 8px; }
    .item-btn { display: flex; align-items: center; gap: 8px; width: 100%; padding: 7px 10px; border: 1px solid #eee; border-radius: 8px; background: white; margin-bottom: 4px; cursor: pointer; font-size: 12px; color: #333; text-align: left; transition: all 0.1s; }
    .item-btn:hover { border-color: #a5d6a7; background: #f1f8f1; }
    .item-btn.selected { border-color: #4caf50; background: #e8f5e9; font-weight: 500; }
    .item-icon { font-size: 16px; width: 22px; text-align: center; }

    #props-panel { padding: 10px 12px; border-top: 1px solid #eee; background: #fafafa; }
    #props-panel h2 { font-size: 12px; font-weight: 600; color: #666; text-transform: uppercase; letter-spacing: 0.05em; margin-bottom: 8px; }
    #sel-info { font-size: 12px; color: #888; margin-bottom: 6px; }
    .prop-row { display: flex; align-items: center; gap: 6px; margin-bottom: 6px; }
    .prop-row label { font-size: 11px; color: #777; width: 52px; }
    .prop-row input[type=number], .prop-row select { flex: 1; font-size: 11px; padding: 4px 6px; border: 1px solid #ddd; border-radius: 5px; height: 28px; }
    .prop-row input[type=color] { flex: 1; height: 28px; padding: 1px; border: 1px solid #ddd; border-radius: 5px; cursor: pointer; }
    .del-btn { width: 100%; margin-top: 4px; font-size: 12px; padding: 5px 10px; border: 1px solid #ffcdd2; border-radius: 6px; background: white; cursor: pointer; color: #c62828; }
    .del-btn:hover { background: #ffebee; }

    /* Canvas area */
    #canvas-area { flex: 1; display: flex; flex-direction: column; }
    #view-tabs { display: flex; background: white; border-bottom: 1px solid #ddd; padding: 0 14px; }
    .view-tab { font-size: 13px; padding: 9px 16px; border: none; background: transparent; cursor: pointer; color: #666; border-bottom: 2px solid transparent; margin-bottom: -1px; }
    .view-tab.active { color: #2e7d32; border-bottom-color: #2e7d32; font-weight: 600; }

    #size-row { display: flex; align-items: center; gap: 8px; padding: 6px 14px; background: white; border-bottom: 1px solid #eee; font-size: 12px; color: #555; }
    #size-row input { width: 58px; font-size: 12px; padding: 3px 6px; border: 1px solid #ddd; border-radius: 5px; height: 26px; }
    #size-hint { margin-left: auto; font-size: 11px; color: #aaa; }

    #canvas-wrap { flex: 1; position: relative; overflow: hidden; background: #c8e6c9; }
    canvas { display: block; }

    #info-bar { display: flex; gap: 20px; padding: 5px 14px; background: white; border-top: 1px solid #eee; font-size: 11px; color: #777; }

    /* Legend */
    #legend { position: absolute; bottom: 8px; right: 8px; background: rgba(255,255,255,0.9); border: 1px solid #ddd; border-radius: 8px; padding: 8px 12px; font-size: 10px; color: #555; pointer-events: none; }
    #legend div { margin-bottom: 2px; }
    #legend span { font-weight: 600; color: #333; }
  </style>
</head>
<body>

<header>
  <span style="font-size:22px;">🌿</span>
  <h1>Tuinontwerp App</h1>
  <span>Ontwerp je droomtuin</span>
  <div class="header-actions">
    <button class="header-btn" onclick="clearAll()">Leegmaken</button>
    <button class="header-btn" onclick="saveAsPNG()">Opslaan als PNG</button>
  </div>
</header>

<div id="main">
  <div id="sidebar">
    <div id="sidebar-header">
      <h2>Elementen</h2>
      <div class="tab-row">
        <button class="tab active" onclick="setTab('planten',this)">Planten</button>
        <button class="tab" onclick="setTab('tegels',this)">Tegels</button>
        <button class="tab" onclick="setTab('overig',this)">Overig</button>
      </div>
    </div>
    <div id="item-list"></div>
    <div id="props-panel">
      <h2>Eigenschappen</h2>
      <div id="sel-info">Niets geselecteerd</div>
      <div id="sel-props" style="display:none;">
        <div class="prop-row"><label>Breedte</label><input type="number" id="prop-w" min="10" max="500" onchange="updateSel()"></div>
        <div class="prop-row"><label>Hoogte</label><input type="number" id="prop-h" min="10" max="500" onchange="updateSel()"></div>
        <div class="prop-row"><label>Kleur</label><input type="color" id="prop-col" onchange="updateSel()"></div>
        <button class="del-btn" onclick="deleteSelected()">🗑 Verwijder element</button>
      </div>
    </div>
  </div>

  <div id="canvas-area">
    <div id="view-tabs">
      <button class="view-tab active" onclick="setView('2d',this)">2D Plattegrond</button>
      <button class="view-tab" onclick="setView('3d',this)">3D Weergave</button>
    </div>
    <div id="size-row">
      <span>Tuin:</span>
      <input type="number" id="gar-w" value="800" min="200" max="2000" onchange="resizeGarden()"> cm breed
      <span>×</span>
      <input type="number" id="gar-h" value="500" min="200" max="2000" onchange="resizeGarden()"> cm diep
      <span id="size-hint">Klik om te plaatsen · Sleep om te verplaatsen</span>
    </div>
    <div id="canvas-wrap">
      <canvas id="canvas2d"></canvas>
      <div id="legend">
        <div><span>Klik</span> element → plaatsen</div>
        <div><span>Sleep</span> → verplaatsen</div>
        <div><span>Klik</span> geplaatst element → selecteren</div>
        <div><span>Esc</span> → deselecteren</div>
      </div>
    </div>
    <div id="info-bar">
      <span id="info-pos">Positie: —</span>
      <span id="info-sel">Geselecteerd: —</span>
      <span id="info-count">Elementen: 0</span>
    </div>
  </div>
</div>

<script>
const ITEMS = {
  planten: [
    {id:'boom',      label:'Boom',       icon:'🌳', color:'#2e7d32', w:60, h:60, shape:'circle'},
    {id:'boom2',     label:'Fruitboom',  icon:'🌲', color:'#388e3c', w:50, h:50, shape:'circle'},
    {id:'struik',    label:'Struik',     icon:'🌿', color:'#43a047', w:35, h:35, shape:'circle'},
    {id:'roos',      label:'Roos',       icon:'🌹', color:'#c2185b', w:25, h:25, shape:'circle'},
    {id:'heg',       label:'Heg',        icon:'🌾', color:'#1b5e20', w:100,h:28, shape:'rect'},
    {id:'gras',      label:'Gazon',      icon:'🟩', color:'#66bb6a', w:140,h:100,shape:'rect'},
    {id:'bloemen',   label:'Bloemen',    icon:'🌸', color:'#f48fb1', w:30, h:30, shape:'circle'},
    {id:'bamboe',    label:'Bamboe',     icon:'🎋', color:'#558b2f', w:20, h:70, shape:'rect'},
    {id:'lavendel',  label:'Lavendel',   icon:'💜', color:'#9575cd', w:30, h:30, shape:'circle'},
    {id:'zonnebloem',label:'Zonnebloem', icon:'🌻', color:'#f9a825', w:25, h:25, shape:'circle'},
    {id:'palm',      label:'Palmplant',  icon:'🌴', color:'#00695c', w:40, h:40, shape:'circle'},
    {id:'cactus',    label:'Cactus',     icon:'🌵', color:'#2e7d32', w:20, h:40, shape:'rect'},
  ],
  tegels: [
    {id:'beton',    label:'Betontegel', icon:'⬜', color:'#9e9e9e', w:80, h:80, shape:'rect', pattern:'tiles'},
    {id:'klinker',  label:'Klinker',    icon:'🟥', color:'#b71c1c', w:80, h:80, shape:'rect', pattern:'tiles'},
    {id:'vlonder',  label:'Vlonder',    icon:'🟫', color:'#6d4c41', w:120,h:70, shape:'rect', pattern:'planks'},
    {id:'grind',    label:'Grind',      icon:'◽', color:'#bdbdbd', w:90, h:90, shape:'rect', pattern:'dots'},
    {id:'grasstegel',label:'Grasstegel',icon:'🟩', color:'#4caf50', w:70, h:70, shape:'rect', pattern:'grass'},
    {id:'siertegel',label:'Siertegel',  icon:'🔷', color:'#1565c0', w:70, h:70, shape:'rect', pattern:'diamond'},
    {id:'split',    label:'Split',      icon:'◾', color:'#757575', w:100,h:100,shape:'rect', pattern:'dots'},
    {id:'houtchips',label:'Houtchips',  icon:'🟤', color:'#8d6e63', w:100,h:100,shape:'rect', pattern:'chips'},
  ],
  overig: [
    {id:'vijver',   label:'Vijver',      icon:'💧', color:'#1565c0', w:110, h:80, shape:'ellipse'},
    {id:'zwemvijver',label:'Zwemvijver', icon:'🏊', color:'#00838f', w:180, h:90, shape:'rect'},
    {id:'terras',   label:'Terras',      icon:'🪑', color:'#a1887f', w:130, h:110, shape:'rect'},
    {id:'schuur',   label:'Schuur',      icon:'🏠', color:'#8d6e63', w:90, h:70, shape:'rect'},
    {id:'kas',      label:'Kas',         icon:'🏗', color:'#b2dfdb', w:100, h:80, shape:'rect'},
    {id:'pad',      label:'Pad',         icon:'〰️', color:'#d7ccc8', w:200, h:30, shape:'rect'},
    {id:'fontein',  label:'Fontein',     icon:'⛲', color:'#0288d1', w:45, h:45, shape:'circle'},
    {id:'sprinkler',label:'Sprinkler',   icon:'💦', color:'#039be5', w:20, h:20, shape:'circle'},
    {id:'schommel', label:'Schommel',    icon:'🎠', color:'#ff8f00', w:65, h:45, shape:'rect'},
    {id:'bbq',      label:'BBQ',         icon:'🔥', color:'#424242', w:35, h:35, shape:'circle'},
    {id:'bank',     label:'Tuinbank',    icon:'🪵', color:'#795548', w:70, h:30, shape:'rect'},
    {id:'trampoline',label:'Trampoline', icon:'⭕', color:'#e53935', w:80, h:80, shape:'ellipse'},
  ]
};

let currentTab = 'planten';
let currentItem = null;
let objects = [];
let selectedId = null;
let dragging = false;
let dragOffX = 0, dragOffY = 0;
let view = '2d';
let garW = 800, garH = 500;
let idCounter = 0;

const canvas2d = document.getElementById('canvas2d');
const ctx = canvas2d.getContext('2d');
const wrap = document.getElementById('canvas-wrap');

function getOffset() {
  return {
    ox: Math.max(20, (canvas2d.width - garW) / 2),
    oy: Math.max(20, (canvas2d.height - garH) / 2)
  };
}

function resizeCanvas() {
  canvas2d.width = wrap.clientWidth;
  canvas2d.height = wrap.clientHeight;
  draw();
}

function resizeGarden() {
  garW = parseInt(document.getElementById('gar-w').value) || 800;
  garH = parseInt(document.getElementById('gar-h').value) || 500;
  draw();
}

function setTab(tab, el) {
  currentTab = tab;
  currentItem = null;
  canvas2d.style.cursor = 'default';
  document.querySelectorAll('.tab').forEach(t => t.classList.remove('active'));
  el.classList.add('active');
  renderItemList();
}

function renderItemList() {
  const list = document.getElementById('item-list');
  list.innerHTML = '';
  ITEMS[currentTab].forEach(item => {
    const btn = document.createElement('button');
    btn.className = 'item-btn' + (currentItem && currentItem.id === item.id ? ' selected' : '');
    btn.innerHTML = `<span class="item-icon">${item.icon}</span><span>${item.label}</span>`;
    btn.onclick = () => {
      currentItem = item;
      canvas2d.style.cursor = 'copy';
      document.querySelectorAll('.item-btn').forEach(b => b.classList.remove('selected'));
      btn.classList.add('selected');
    };
    list.appendChild(btn);
  });
}

function setView(v, el) {
  view = v;
  document.querySelectorAll('.view-tab').forEach(t => t.classList.remove('active'));
  el.classList.add('active');
  draw();
}

// ── DRAW ──────────────────────────────────────────────────────────
function draw() {
  if (view === '2d') draw2d();
  else draw3d();
}

function draw2d() {
  const cw = canvas2d.width, ch = canvas2d.height;
  const {ox, oy} = getOffset();
  ctx.clearRect(0, 0, cw, ch);

  // background
  ctx.fillStyle = '#c8e6c9';
  ctx.fillRect(0, 0, cw, ch);

  // garden fill
  ctx.fillStyle = '#a5d6a7';
  ctx.fillRect(ox, oy, garW, garH);

  // grid
  ctx.strokeStyle = 'rgba(0,0,0,0.06)';
  ctx.lineWidth = 0.5;
  for (let x = ox; x <= ox + garW; x += 50) { ctx.beginPath(); ctx.moveTo(x, oy); ctx.lineTo(x, oy + garH); ctx.stroke(); }
  for (let y = oy; y <= oy + garH; y += 50) { ctx.beginPath(); ctx.moveTo(ox, y); ctx.lineTo(ox + garW, y); ctx.stroke(); }

  // border
  ctx.strokeStyle = '#4caf50';
  ctx.lineWidth = 2;
  ctx.strokeRect(ox, oy, garW, garH);

  // scale bar
  ctx.fillStyle = 'rgba(0,0,0,0.4)';
  ctx.fillRect(ox + 10, oy + garH - 22, 100, 4);
  ctx.fillStyle = 'rgba(0,0,0,0.6)';
  ctx.font = '10px system-ui';
  ctx.textAlign = 'left';
  ctx.fillText('100 cm', ox + 10, oy + garH - 8);

  // objects
  objects.forEach(obj => {
    ctx.save();
    if (obj.id === selectedId) {
      ctx.shadowColor = '#1976d2';
      ctx.shadowBlur = 10;
    }
    drawObject2d(ctx, obj, ox, oy);
    ctx.restore();
    if (obj.id === selectedId) {
      ctx.strokeStyle = '#1976d2';
      ctx.lineWidth = 2;
      ctx.setLineDash([4, 3]);
      ctx.strokeRect(ox + obj.x - 3, oy + obj.y - 3, obj.w + 6, obj.h + 6);
      ctx.setLineDash([]);
    }
    ctx.fillStyle = 'rgba(0,0,0,0.5)';
    ctx.font = '10px system-ui';
    ctx.textAlign = 'center';
    ctx.fillText(obj.label, ox + obj.x + obj.w / 2, oy + obj.y + obj.h + 13);
  });

  document.getElementById('info-count').textContent = 'Elementen: ' + objects.length;
}

function drawObject2d(ctx, obj, ox, oy) {
  ctx.fillStyle = obj.color;
  const cx = ox + obj.x + obj.w / 2, cy = oy + obj.y + obj.h / 2;

  if (obj.shape === 'circle' || obj.shape === 'ellipse') {
    ctx.beginPath();
    ctx.ellipse(cx, cy, obj.w / 2, obj.h / 2, 0, 0, Math.PI * 2);
    ctx.fill();
    if (obj.id === 'vijver' || obj.id === 'zwemvijver' || obj.id === 'fontein') {
      ctx.strokeStyle = 'rgba(255,255,255,0.5)';
      ctx.lineWidth = 1.5;
      ctx.stroke();
    }
  } else {
    ctx.fillRect(ox + obj.x, oy + obj.y, obj.w, obj.h);
    drawPattern(ctx, obj, ox, oy);
  }

  ctx.font = Math.min(obj.w, obj.h) > 30 ? '18px serif' : '12px serif';
  ctx.textAlign = 'center';
  ctx.textBaseline = 'middle';
  ctx.fillText(obj.icon, cx, cy);
  ctx.textBaseline = 'alphabetic';
}

function drawPattern(ctx, obj, ox, oy) {
  const x0 = ox + obj.x, y0 = oy + obj.y;
  ctx.save();
  ctx.beginPath();
  ctx.rect(x0, y0, obj.w, obj.h);
  ctx.clip();

  switch (obj.pattern) {
    case 'tiles':
      ctx.strokeStyle = 'rgba(0,0,0,0.18)';
      ctx.lineWidth = 0.5;
      for (let x = x0; x <= x0 + obj.w; x += 22) { ctx.beginPath(); ctx.moveTo(x, y0); ctx.lineTo(x, y0 + obj.h); ctx.stroke(); }
      for (let y = y0; y <= y0 + obj.h; y += 22) { ctx.beginPath(); ctx.moveTo(x0, y); ctx.lineTo(x0 + obj.w, y); ctx.stroke(); }
      break;
    case 'planks':
      ctx.strokeStyle = 'rgba(0,0,0,0.2)';
      ctx.lineWidth = 0.5;
      for (let y = y0; y <= y0 + obj.h; y += 16) { ctx.beginPath(); ctx.moveTo(x0, y); ctx.lineTo(x0 + obj.w, y); ctx.stroke(); }
      break;
    case 'dots':
    case 'chips':
      ctx.fillStyle = 'rgba(0,0,0,0.15)';
      for (let dx = x0 + 6; dx < x0 + obj.w; dx += 10)
        for (let dy = y0 + 6; dy < y0 + obj.h; dy += 10) {
          ctx.beginPath(); ctx.arc(dx, dy, 1.5, 0, Math.PI * 2); ctx.fill();
        }
      break;
    case 'diamond':
      ctx.strokeStyle = 'rgba(255,255,255,0.3)';
      ctx.lineWidth = 0.5;
      for (let d = -obj.h; d < obj.w + obj.h; d += 22) {
        ctx.beginPath(); ctx.moveTo(x0 + d, y0); ctx.lineTo(x0 + d + obj.h, y0 + obj.h); ctx.stroke();
        ctx.beginPath(); ctx.moveTo(x0 + d + obj.h, y0); ctx.lineTo(x0 + d, y0 + obj.h); ctx.stroke();
      }
      break;
  }
  ctx.restore();
}

function draw3d() {
  const cw = canvas2d.width, ch = canvas2d.height;
  ctx.clearRect(0, 0, cw, ch);

  // Sky gradient substitute
  ctx.fillStyle = '#e3f2fd';
  ctx.fillRect(0, 0, cw, ch / 2);
  ctx.fillStyle = '#c8e6c9';
  ctx.fillRect(0, ch / 2, cw, ch / 2);

  const scale = Math.min(0.45, Math.min(cw / (garW * 1.4), ch / (garH * 1.4)));
  const isoXx = 0.6, isoXy = 0.3, isoYx = -0.6, isoYy = 0.3;
  const baseX = cw / 2, baseY = ch * 0.62;
  const gw = garW * scale, gh = garH * scale;

  function to3d(px, py, pz) {
    return {
      x: baseX + (px - gw / 2) * isoXx + (py - gh / 2) * isoYx,
      y: baseY + (px - gw / 2) * isoXy + (py - gh / 2) * isoYy - pz * scale * 0.8
    };
  }

  // garden floor
  const c0 = to3d(0, 0, 0), c1 = to3d(gw, 0, 0), c2 = to3d(gw, gh, 0), c3 = to3d(0, gh, 0);
  ctx.beginPath();
  ctx.moveTo(c0.x, c0.y); ctx.lineTo(c1.x, c1.y); ctx.lineTo(c2.x, c2.y); ctx.lineTo(c3.x, c3.y);
  ctx.closePath();
  ctx.fillStyle = '#81c784';
  ctx.fill();
  ctx.strokeStyle = '#4caf50';
  ctx.lineWidth = 1.5;
  ctx.stroke();

  // sort back-to-front
  const sorted = [...objects].sort((a, b) => (a.y + a.h) - (b.y + b.h));

  sorted.forEach(obj => {
    const px = (obj.x + obj.w / 2) * scale;
    const py = (obj.y + obj.h / 2) * scale;
    const pw = obj.w * scale;
    const ph = obj.h * scale;
    let elev = 0;

    if (obj.shape === 'circle') elev = pw * 1.2;
    else if (['schuur', 'kas'].includes(obj.id)) elev = 50 * scale;
    else if (['heg', 'bamboe'].includes(obj.id)) elev = ph * 1.5;
    else if (['vlonder', 'terras', 'gras'].includes(obj.id)) elev = 2 * scale;
    else elev = 8 * scale;

    if (obj.shape === 'circle' || obj.shape === 'ellipse') {
      const cp = to3d(px, py, elev);
      // shadow
      const sp = to3d(px, py, 0);
      ctx.beginPath();
      ctx.ellipse(sp.x, sp.y + 2, pw * 0.45, pw * 0.15, 0, 0, Math.PI * 2);
      ctx.fillStyle = 'rgba(0,0,0,0.15)';
      ctx.fill();
      // shape
      ctx.beginPath();
      ctx.ellipse(cp.x, cp.y, pw / 2, ph / 2.5, 0, 0, Math.PI * 2);
      ctx.fillStyle = obj.color;
      ctx.fill();
      ctx.strokeStyle = shadeColor(obj.color, -40);
      ctx.lineWidth = 0.5;
      ctx.stroke();
    } else {
      const tl = to3d(px - pw / 2, py - ph / 2, elev);
      const tr = to3d(px + pw / 2, py - ph / 2, elev);
      const bl = to3d(px - pw / 2, py + ph / 2, elev);
      const br = to3d(px + pw / 2, py + ph / 2, elev);
      const fl = to3d(px - pw / 2, py + ph / 2, 0);
      const fr = to3d(px + pw / 2, py + ph / 2, 0);

      // front face
      ctx.beginPath();
      ctx.moveTo(bl.x, bl.y); ctx.lineTo(fl.x, fl.y);
      ctx.lineTo(fr.x, fr.y); ctx.lineTo(br.x, br.y);
      ctx.closePath();
      ctx.fillStyle = shadeColor(obj.color, -40);
      ctx.fill();

      // top face
      ctx.beginPath();
      ctx.moveTo(tl.x, tl.y); ctx.lineTo(tr.x, tr.y);
      ctx.lineTo(br.x, br.y); ctx.lineTo(bl.x, bl.y);
      ctx.closePath();
      ctx.fillStyle = obj.color;
      ctx.fill();
      ctx.strokeStyle = shadeColor(obj.color, -60);
      ctx.lineWidth = 0.5;
      ctx.stroke();
    }

    // label
    const lp = to3d(px, py, elev + 4 * scale);
    ctx.font = `${Math.max(10, Math.min(pw, ph) * 0.7)}px serif`;
    ctx.textAlign = 'center';
    ctx.fillText(obj.icon, lp.x, lp.y);
  });
}

function shadeColor(col, amt) {
  try {
    let r = parseInt(col.slice(1,3),16), g = parseInt(col.slice(3,5),16), b = parseInt(col.slice(5,7),16);
    r = Math.max(0, Math.min(255, r + amt));
    g = Math.max(0, Math.min(255, g + amt));
    b = Math.max(0, Math.min(255, b + amt));
    return '#' + [r,g,b].map(v => v.toString(16).padStart(2,'0')).join('');
  } catch(e) { return col; }
}

// ── CANVAS EVENTS ─────────────────────────────────────────────────
canvas2d.addEventListener('mousemove', e => {
  const rect = canvas2d.getBoundingClientRect();
  const mx = e.clientX - rect.left, my = e.clientY - rect.top;
  const {ox, oy} = getOffset();
  const gx = Math.round(mx - ox), gy = Math.round(my - oy);
  document.getElementById('info-pos').textContent = `Positie: ${gx}×${gy} cm`;

  if (dragging && selectedId !== null) {
    const obj = objects.find(o => o.id === selectedId);
    if (obj) {
      obj.x = Math.max(0, Math.min(garW - obj.w, mx - ox - dragOffX));
      obj.y = Math.max(0, Math.min(garH - obj.h, my - oy - dragOffY));
      draw2d();
    }
  }
});

canvas2d.addEventListener('mousedown', e => {
  if (view === '3d') return;
  const rect = canvas2d.getBoundingClientRect();
  const mx = e.clientX - rect.left, my = e.clientY - rect.top;
  const {ox, oy} = getOffset();
  const gx = mx - ox, gy = my - oy;

  if (currentItem && gx >= 0 && gx <= garW && gy >= 0 && gy <= garH) {
    const newObj = {
      id: ++idCounter,
      label: currentItem.label,
      icon: currentItem.icon,
      color: currentItem.color,
      w: currentItem.w,
      h: currentItem.h,
      shape: currentItem.shape,
      pattern: currentItem.pattern || null,
      x: Math.max(0, Math.min(garW - currentItem.w, gx - currentItem.w / 2)),
      y: Math.max(0, Math.min(garH - currentItem.h, gy - currentItem.h / 2))
    };
    objects.push(newObj);
    selectedId = newObj.id;
    updatePropsPanel();
    draw2d();
    return;
  }

  let hit = null;
  for (let i = objects.length - 1; i >= 0; i--) {
    const o = objects[i];
    if (gx >= o.x && gx <= o.x + o.w && gy >= o.y && gy <= o.y + o.h) {
      hit = o; dragOffX = gx - o.x; dragOffY = gy - o.y; break;
    }
  }

  if (hit) {
    selectedId = hit.id;
    dragging = true;
    currentItem = null;
    canvas2d.style.cursor = 'move';
    document.querySelectorAll('.item-btn').forEach(b => b.classList.remove('selected'));
    updatePropsPanel();
    draw2d();
  } else {
    selectedId = null;
    updatePropsPanel();
    draw2d();
  }
});

canvas2d.addEventListener('mouseup', () => { dragging = false; canvas2d.style.cursor = currentItem ? 'copy' : 'default'; });
canvas2d.addEventListener('mouseleave', () => { dragging = false; });

document.addEventListener('keydown', e => {
  if (e.key === 'Escape') { currentItem = null; canvas2d.style.cursor = 'default'; document.querySelectorAll('.item-btn').forEach(b => b.classList.remove('selected')); }
  if ((e.key === 'Delete' || e.key === 'Backspace') && selectedId !== null) deleteSelected();
});

// ── PROPS PANEL ────────────────────────────────────────────────────
function updatePropsPanel() {
  const obj = objects.find(o => o.id === selectedId);
  if (obj) {
    document.getElementById('sel-info').textContent = obj.label;
    document.getElementById('sel-props').style.display = '';
    document.getElementById('prop-w').value = obj.w;
    document.getElementById('prop-h').value = obj.h;
    document.getElementById('prop-col').value = obj.color;
    document.getElementById('info-sel').textContent = 'Geselecteerd: ' + obj.label;
  } else {
    document.getElementById('sel-info').textContent = 'Niets geselecteerd';
    document.getElementById('sel-props').style.display = 'none';
    document.getElementById('info-sel').textContent = 'Geselecteerd: —';
  }
}

function updateSel() {
  const obj = objects.find(o => o.id === selectedId);
  if (!obj) return;
  obj.w = parseInt(document.getElementById('prop-w').value) || obj.w;
  obj.h = parseInt(document.getElementById('prop-h').value) || obj.h;
  obj.color = document.getElementById('prop-col').value;
  draw();
}

function deleteSelected() {
  objects = objects.filter(o => o.id !== selectedId);
  selectedId = null;
  updatePropsPanel();
  draw();
}

function clearAll() {
  if (objects.length === 0 || confirm('Weet je zeker dat je de tuin wilt leegmaken?')) {
    objects = [];
    selectedId = null;
    updatePropsPanel();
    draw();
  }
}

function saveAsPNG() {
  const link = document.createElement('a');
  link.download = 'tuinontwerp.png';
  link.href = canvas2d.toDataURL('image/png');
  link.click();
}

// ── INIT ───────────────────────────────────────────────────────────
renderItemList();
window.addEventListener('resize', resizeCanvas);
setTimeout(resizeCanvas, 50);
</script>
</body>
</html>
