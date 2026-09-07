<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>ROBERT</title>
<link rel="preconnect" href="https://fonts.googleapis.com">
<link href="https://fonts.googleapis.com/css2?family=Special+Elite&family=Courier+Prime:wght@400;700&display=swap" rel="stylesheet">
<style>
  :root{
    --bg: #060604;
    --bg-panel: #101109;
    --bone: #c8c3a8;
    --bone-dim: #857f68;
    --blood: #7a1414;
    --blood-bright: #b8281f;
    --line: #2a2b1f;
  }
  *{box-sizing:border-box; margin:0; padding:0;}
  html,body{
    background:var(--bg);
    color:var(--bone);
    font-family:'Courier Prime', monospace;
    min-height:100vh;
    overflow-x:hidden;
  }
  body{
    display:flex; align-items:center; justify-content:center;
    padding:20px; line-height:1.5;
    background-image:
      radial-gradient(ellipse at 50% -10%, rgba(122,20,20,0.08), transparent 60%),
      repeating-linear-gradient(0deg, rgba(255,255,255,0.012) 0px, rgba(255,255,255,0.012) 1px, transparent 1px, transparent 3px);
  }
  .outer{ position:relative; width:100%; max-width:640px; }
  .stage{
    width:100%; min-height:520px;
    background:var(--bg-panel);
    border:1px solid var(--line);
    position:relative;
    padding:32px 28px;
    display:flex; flex-direction:column; justify-content:center;
    box-shadow: 0 0 0 1px rgba(0,0,0,0.4), 0 30px 80px rgba(0,0,0,0.6);
  }
  .vignette{
    position:absolute; inset:0; pointer-events:none;
    box-shadow: inset 0 0 120px 40px rgba(184,40,31,0);
    transition: box-shadow .25s ease;
    z-index:5;
  }
  .flash{
    position:fixed; inset:0; background:#fff; opacity:0;
    pointer-events:none; z-index:80;
  }
  .flash.go{ animation:flashpop .18s ease-out; }
  @keyframes flashpop{ 0%{opacity:0.9;} 100%{opacity:0;} }

  .jumpscare{
    position:fixed; inset:0; z-index:90;
    display:flex; align-items:center; justify-content:center;
    opacity:0; pointer-events:none; background:rgba(0,0,0,0);
  }
  .jumpscare.go{ animation: jumpscarepop 0.32s steps(1); }
  @keyframes jumpscarepop{ 0%{opacity:1; background:rgba(0,0,0,0.75);} 65%{opacity:1; background:rgba(0,0,0,0.75);} 100%{opacity:0; background:rgba(0,0,0,0);} }
  .jumpscare svg{ width:64%; max-width:320px; filter:drop-shadow(0 0 45px rgba(184,40,31,0.7)); }

  .flicker{ animation:flicker 6s infinite; }
  @keyframes flicker{
    0%,93%,100%{opacity:1;} 94%{opacity:0.85;} 95%{opacity:1;} 96%{opacity:0.65;} 97%{opacity:1;}
  }
  h1.title{ font-family:'Special Elite', monospace; font-size:2.1rem; letter-spacing:2px; margin-bottom:10px;}
  h2.heading{ font-family:'Special Elite', monospace; font-size:1.25rem; letter-spacing:1px; margin-bottom:16px;}
  p.body-text{ font-size:1rem; margin-bottom:14px;}
  p.dim{ color:var(--bone-dim); font-size:0.9rem; }
  .btn-row{ display:flex; flex-direction:column; gap:10px; margin-top:20px; }
  button.choice{
    background:transparent; border:1px solid var(--line); color:var(--bone);
    font-family:'Courier Prime', monospace; font-size:0.96rem; padding:14px 16px;
    text-align:left; cursor:pointer; transition:border-color .15s, background .15s, transform .1s;
  }
  button.choice:hover{ border-color:var(--blood-bright); background:rgba(122,20,20,0.08); }
  button.choice:active{ transform:translateY(1px); }
  button.primary{
    background:var(--blood); border:1px solid var(--blood-bright); color:#e9e2cf;
    font-family:'Special Elite', monospace; letter-spacing:1px; padding:13px 20px;
    cursor:pointer; align-self:flex-start; margin-top:16px;
  }
  button.primary:hover{ background:var(--blood-bright); }

  .progress{ display:flex; gap:6px; margin-bottom:22px; }
  .dot{ width:7px; height:7px; border-radius:50%; background:var(--line); }
  .dot.done{ background:var(--bone-dim); }
  .dot.now{ background:var(--blood-bright); }

  .anomaly{ width:100%; height:210px; margin:8px 0 16px 0; position:relative; background:#050403; border:1px solid var(--line); overflow:hidden; }
  .anomaly svg{ width:100%; height:100%; display:block; }
  .static-overlay{
    position:absolute; inset:0;
    background-image:repeating-linear-gradient(0deg, rgba(255,255,255,0.04) 0px, transparent 1px, transparent 2px);
    mix-blend-mode:overlay; animation:staticmove .2s steps(2) infinite; pointer-events:none;
  }
  @keyframes staticmove{ 0%{transform:translateY(0);} 100%{transform:translateY(2px);} }

  .blackout{ position:fixed; inset:0; background:#000; display:flex; align-items:center; justify-content:center; z-index:50; }
  .blackout span{ font-family:'Special Elite', monospace; color:var(--blood-bright); font-size:2rem; letter-spacing:4px; animation:pulse 1.4s ease-in-out infinite; }
  @keyframes pulse{ 0%,100%{opacity:.35;} 50%{opacity:1;} }

  .hud{ display:flex; justify-content:space-between; align-items:flex-start; margin-bottom:10px; font-size:0.82rem; color:var(--bone-dim); }
  .hud .time{ color:var(--bone); font-family:'Special Elite',monospace; letter-spacing:1px; }
  .danger-label{ text-align:right; }
  .inventory{ display:flex; gap:8px; margin-bottom:10px; font-size:0.78rem; flex-wrap:wrap; }
  .inv-item{ border:1px solid var(--line); padding:3px 8px; color:var(--bone-dim); }
  .inv-item.got{ color:var(--bone); border-color:#4c5a34; }

  .map-wrap{ position:relative; border:1px solid var(--line); background:#040403; overflow:hidden; }
  #mapContainer{ width:100%; }
  #mapContainer canvas{ display:block; width:100% !important; height:100% !important; }

  .search-btn-wrap{ display:flex; justify-content:center; margin-top:10px; }
  .search-btn{
    background:var(--blood); border:1px solid var(--blood-bright); color:#e9e2cf;
    font-family:'Special Elite',monospace; letter-spacing:1px; padding:10px 22px; cursor:pointer;
  }
  .search-btn:disabled{ background:transparent; border-color:var(--line); color:var(--bone-dim); cursor:default; }

  .nearby{ text-align:center; font-size:0.78rem; color:var(--bone-dim); margin-top:6px; min-height:16px; }
  .log{ margin-top:10px; font-size:0.82rem; color:var(--bone-dim); min-height:36px; border-left:2px solid var(--blood); padding-left:10px; }
  .controls-hint{ margin-top:8px; font-size:0.72rem; color:var(--bone-dim); text-align:center; letter-spacing:0.5px; }
  .credit{ margin-top:24px; font-size:0.7rem; color:var(--bone-dim); letter-spacing:1px; }
  ::selection{ background:var(--blood); color:#fff; }
</style>
</head>
<body>

<div class="flash" id="flash"></div>
<div class="jumpscare" id="jumpscareEl"></div>
<div class="outer">
  <div class="stage" id="stage">
    <div class="vignette" id="vignette"></div>
  </div>
</div>

<script src="https://cdnjs.cloudflare.com/ajax/libs/three.js/r128/three.min.js"></script>
<script>
/* =========================================================
   AUDIO ENGINE — everything synthesized, no external files
========================================================= */
let actx = null;
function ensureAudio(){
  if(!actx){ try{ actx = new (window.AudioContext||window.webkitAudioContext)(); }catch(e){ actx=null; } }
  if(actx && actx.state === 'suspended') actx.resume();
}
function playTone(freq, duration, type='sine', gainVal=0.15, delay=0){
  if(!actx) return;
  const osc = actx.createOscillator(); const gain = actx.createGain();
  osc.type = type; osc.frequency.value = freq;
  osc.connect(gain).connect(actx.destination);
  const t0 = actx.currentTime + delay;
  gain.gain.setValueAtTime(0.0001, t0);
  gain.gain.linearRampToValueAtTime(gainVal, t0+0.02);
  gain.gain.exponentialRampToValueAtTime(0.0001, t0+duration);
  osc.start(t0); osc.stop(t0+duration+0.05);
}
function playNoiseBurst(duration=0.4, gainVal=0.25){
  if(!actx) return;
  const bufferSize = Math.floor(actx.sampleRate*duration);
  const buffer = actx.createBuffer(1, bufferSize, actx.sampleRate);
  const data = buffer.getChannelData(0);
  for(let i=0;i<bufferSize;i++){ data[i] = (Math.random()*2-1) * (1 - i/bufferSize); }
  const noise = actx.createBufferSource(); noise.buffer = buffer;
  const gain = actx.createGain(); gain.gain.value = gainVal;
  noise.connect(gain).connect(actx.destination);
  noise.start();
}
function playStinger(){
  playNoiseBurst(0.55, 0.32);
  playTone(58,0.65,'sawtooth',0.22);
  playTone(170,0.3,'square',0.10,0.04);
}
function playFootstepBurst(gainVal){ playNoiseBurst(0.12, Math.max(0.02, Math.min(0.28,gainVal))); }

let heartbeatTimer=null, heartbeatRate=1400, heartbeatOn=false;
function beatOnce(){ playTone(52,0.13,'sine',0.28); playTone(42,0.15,'sine',0.20,0.14); }
function startHeartbeat(rate=1400){
  heartbeatOn = true; heartbeatRate = rate; clearTimeout(heartbeatTimer);
  const loop = ()=>{ if(!heartbeatOn) return; beatOnce(); heartbeatTimer = setTimeout(loop, heartbeatRate); };
  loop();
}
function setHeartbeatRate(r){ heartbeatRate = r; }
function stopHeartbeat(){ heartbeatOn=false; clearTimeout(heartbeatTimer); }

function flashScreen(){ const f = document.getElementById('flash'); f.classList.remove('go'); void f.offsetWidth; f.classList.add('go'); }
function setVignette(intensity){
  const v = document.getElementById('vignette'); if(!v) return;
  const spread = 30 + intensity*80, blur = 90 + intensity*60;
  v.style.boxShadow = `inset 0 0 ${blur}px ${spread}px rgba(184,40,31,${intensity*0.55})`;
}

/* ---------------- jumpscare face flash ---------------- */
function triggerJumpscare(){
  const el = document.getElementById('jumpscareEl');
  if(!el) return;
  el.innerHTML = jumpscareFaceSVG();
  el.classList.remove('go'); void el.offsetWidth; el.classList.add('go');
  playStinger();
  const mapWrap = document.querySelector('.map-wrap');
  if(mapWrap){
    mapWrap.style.transform = `translate(${(Math.random()-0.5)*16}px, ${(Math.random()-0.5)*16}px)`;
    setTimeout(()=>{ if(mapWrap) mapWrap.style.transform = 'none'; }, 260);
  }
}

/* =========================================================
   STATE
========================================================= */
const state = { screen:'title', quizIndex:0, partsFound:[], hasAxe:false, timeLeft:200, rafHandle:null, lastLog:"" };
const PARTS_NEEDED = ['head','arm','leg'];

/* =========================================================
   QUIZ CONTENT
========================================================= */
const quiz = [
  { hint: "I have a long neck so I can reach leaves nothing else can. What am I?", options: ["Giraffe","Elephant","Ostrich","Camel"] },
  { hint: "I carry my young in a pouch and cover long distances in a single hop.", options: ["Kangaroo","Rabbit","Frog","Wallaby"] },
  { hint: "I've been called man's best friend since before either of us can remember.", options: ["Dog","Wolf","Horse","Cat"] },
  { hint: "I'm awake while you sleep. I watch the house from the trees.", options: ["Owl","Bat","Raccoon","Fox"] },
  { hint: "I spin something invisible in the corners of rooms you don't check anymore.", options: ["Spider","Moth","Beetle","Wasp"] },
  { hint: "I remember faces. I remember yours especially.", options: ["Crow","Elephant","Dolphin","Pig"] },
  { hint: "I hunt after dark, and I'm patient about it.", options: ["Wolf","Coyote","Lynx","Jackal"] },
  { hint: "I like to play hide and seek in the woods behind your house. I always know where you are.", options: ["Fox","Deer","Rabbit","I'd rather not say"] },
  { hint: "I know your daily routine better than you do.", options: ["Cat","A neighbor","Habit, maybe","Something else"] },
  { hint: "I used to have a mother. Now I don't. I have four legs. Sometimes two.", options: ["Deer","Robert","A memory","Nothing, forget it"] },
  { hint: "We used to play in the treehouse every summer, just the two of us.", options: ["A childhood friend","Robert","A raccoon","This isn't about an animal anymore, is it"] },
  { hint: "I stopped answering my phone three days ago.", options: ["Turtle — he hides in his shell","Robert","A busy week","Please stop asking"] },
  { hint: "I am not in my bed tonight.", options: ["Snake — sheds and moves on","Robert"] },
  { hint: "I am not an animal. I never was. Do you still want to guess?", options: ["Yes","No, I want to stop"] },
  { hint: "Do you recognize this?", options: ["Robert"], isImage: true }
];

const stage = document.getElementById('stage');
function render(html){ stage.innerHTML = `<div class="vignette" id="vignette"></div>` + html; }

/* =========================================================
   TITLE
========================================================= */
function screenTitle(){
  setVignette(0);
  render(`
    <h1 class="title flicker">A FEW QUESTIONS</h1>
    <p class="body-text dim">A short quiz. Nothing more. Sound is part of this — turn it on if you can.</p>
    <button class="primary" onclick="ensureAudio(); startQuiz();">Begin</button>
    <p class="credit">15 questions. Answer honestly.</p>
  `);
}

/* =========================================================
   QUIZ
========================================================= */
function startQuiz(){ state.quizIndex = 0; screenQuiz(); }

function screenQuiz(){
  const i = state.quizIndex; const q = quiz[i];
  const dots = quiz.map((_,idx)=>{
    const cls = idx < i ? 'done' : (idx===i ? 'now' : '');
    return `<div class="dot ${cls}"></div>`;
  }).join('');
  let imageBlock = '';
  if(q.isImage){ imageBlock = `<div class="anomaly">${anomalySVG()}<div class="static-overlay"></div></div>`; }
  const optionsHtml = q.options.map(opt =>
    `<button class="choice" onclick="answerQuiz('${opt.replace(/'/g,"\\'")}')">${opt}</button>`
  ).join('');
  render(`
    <div class="progress">${dots}</div>
    <h2 class="heading">${q.isImage ? '' : 'Question ' + (i+1) + ' of 15'}</h2>
    ${imageBlock}
    <p class="body-text">${q.hint}</p>
    <div class="btn-row">${optionsHtml}</div>
  `);
  if(q.isImage) setVignette(0.35);
}

/* ---------------- low-poly face generator (used everywhere) ---------------- */
function lowPolyFace(base, shade, eye){
  return `
    <polygon points="150,15 95,55 205,55" fill="${base}"/>
    <polygon points="95,55 205,55 150,150" fill="${shade}"/>
    <polygon points="95,55 150,150 55,150" fill="${base}"/>
    <polygon points="205,55 245,150 150,150" fill="${shade}"/>
    <polygon points="55,150 150,150 95,255" fill="${shade}"/>
    <polygon points="150,150 245,150 205,255" fill="${base}"/>
    <polygon points="150,150 95,255 205,255" fill="${shade}"/>
    <polygon points="95,255 205,255 150,305" fill="${base}"/>
    <polygon points="118,140 138,132 132,158" fill="${eye}"/>
    <polygon points="182,140 162,132 168,158" fill="${eye}"/>
    <polygon points="130,230 170,230 150,245" fill="${eye}"/>
  `;
}

function anomalySVG(){
  return `
  <svg viewBox="0 0 300 320" preserveAspectRatio="xMidYMid slice">
    <defs>
      <radialGradient id="g1" cx="50%" cy="35%" r="65%">
        <stop offset="0%" stop-color="#1c1610"/><stop offset="100%" stop-color="#050504"/>
      </radialGradient>
      <filter id="rough">
        <feTurbulence type="fractalNoise" baseFrequency="0.01 0.06" numOctaves="2" seed="7" result="n"/>
        <feDisplacementMap in="SourceGraphic" in2="n" scale="10"/>
      </filter>
    </defs>
    <rect width="300" height="320" fill="url(#g1)"/>
    <g filter="url(#rough)" opacity="0.95">
      ${lowPolyFace('#cdc7ab','#a49d7e','#0c0a08')}
    </g>
  </svg>`;
}

function killerSVG(){
  return `
  <svg viewBox="0 0 300 320" preserveAspectRatio="xMidYMid slice">
    <defs>
      <radialGradient id="g2" cx="50%" cy="30%" r="65%">
        <stop offset="0%" stop-color="#170a0a"/><stop offset="100%" stop-color="#050403"/>
      </radialGradient>
      <filter id="rough2">
        <feTurbulence type="fractalNoise" baseFrequency="0.015 0.08" numOctaves="2" seed="4" result="n"/>
        <feDisplacementMap in="SourceGraphic" in2="n" scale="12"/>
      </filter>
    </defs>
    <rect width="300" height="320" fill="url(#g2)"/>
    <g filter="url(#rough2)" opacity="0.95">
      ${lowPolyFace('#8a2e2e','#5c1414','#050302')}
    </g>
  </svg>`;
}

function jumpscareFaceSVG(){
  return `
  <svg viewBox="0 0 300 320">
    <g>${lowPolyFace('#e2261c','#7a0e0e','#000000')}</g>
  </svg>`;
}

function answerQuiz(answer){
  const i = state.quizIndex;
  if(i === quiz.length - 1){
    flashScreen(); playStinger();
    setTimeout(screenRobDialogue, 200);
    return;
  }
  state.quizIndex++;
  screenQuiz();
}

function screenRobDialogue(){
  render(`
    <div class="anomaly">${anomalySVG()}<div class="static-overlay"></div></div>
    <p class="body-text">"...Rob? Is that you?"</p>
    <div class="btn-row"><button class="primary" onclick="screenBlackout()">—</button></div>
  `);
  setVignette(0.45);
}

function screenBlackout(){
  stage.parentElement.insertAdjacentHTML('beforeend', `<div class="blackout" id="bo"><span>SAVE ME</span></div>`);
  playStinger(); startHeartbeat(900);
  setTimeout(()=>{
    stopHeartbeat();
    const bo = document.getElementById('bo'); if(bo) bo.remove();
    screenPartTwoIntro();
  }, 3400);
}

/* =========================================================
   PART TWO — SETUP
========================================================= */
function screenPartTwoIntro(){
  setVignette(0.1);
  render(`
    <h2 class="heading">Behind the House</h2>
    <p class="body-text">The message is still burning behind your eyes when you realize where it came from.
    The forest starts at the edge of your backyard, past the fence your dad keeps meaning to fix.</p>
    <p class="body-text">Robert's out there. Somewhere in the dark, he needs you.</p>
    <p class="body-text dim">You grab a flashlight and go.</p>
    <button class="primary" onclick="screenObjective()">Enter the forest</button>
  `);
}
function screenObjective(){
  render(`
    <h2 class="heading">Objective: Find Robert</h2>
    <p class="body-text">The trees close in fast behind you. Your flashlight beam shakes more than you'd like to admit.</p>
    <button class="primary" onclick="screenFoundRobert()">Keep going</button>
  `);
}
function screenFoundRobert(){
  render(`
    <h2 class="heading">A Clearing</h2>
    <p class="body-text">You find him in a clearing you don't remember ever seeing before. What's left of him.</p>
    <p class="body-text">"Robert... who could've done this to you..."</p>
    <button class="primary" onclick="screenKillerReveal()">—</button>
  `);
  setVignette(0.3);
}
function screenKillerReveal(){
  flashScreen(); playStinger();
  render(`
    <div class="anomaly">${killerSVG()}<div class="static-overlay"></div></div>
    <p class="body-text">Behind a tree, a man stands watching you. Where his head should be, there's hers —
    Robert's mother. She's been dead three weeks.</p>
    <p class="body-text dim">This was never a rescue. It was bait, and you walked right into it.</p>
    <p class="body-text">"If you find Robert's other parts and arrange them," the man says, "you can go home.
    You don't have long before it's fully dark."</p>
    <button class="primary" onclick="startSearchPhase()">Step into the trees</button>
  `);
  setVignette(0.45);
}

/* =========================================================
   THE MOVABLE MAP — first-person 3D forest (Three.js)
   World coordinates: x = east/west, z = north/south, y = up
========================================================= */
const MAP_W = 560, MAP_H = 340;
const LOCATIONS_LAYOUT = [
  {name:"The Old Well",       x:-210, z:-100},
  {name:"Broken Treehouse",   x:0,    z:-125},
  {name:"The Riverbank",      x:205,  z:-90},
  {name:"Hollow Log",         x:-225, z:40},
  {name:"The Toolshed",       x:215,  z:55},
  {name:"The Dead Oak",       x:-130, z:120},
  {name:"Rocky Overhang",     x:120,  z:120},
  {name:"The Clearing Edge",  x:0,    z:5}
];
const PLAYER_START = { x:0, z:150 };
const EXIT_POS = { x:0, z:158 };
const ALTAR_POS = { x:0, z:-60 };
const KILLER_START = { x:-250, z:-140 };
const EYE_HEIGHT = 14;

const SCARES = [
  "Just branches snapping in the wind. Probably.",
  "You hear something breathing behind you. When you turn — nothing.",
  "A twig snaps, close. Too close.",
  "Something pale moves between the trees and is gone.",
  "The mother's voice, humming. It shouldn't still be able to do that."
];
function shuffle(arr){
  const a = arr.slice();
  for(let i=a.length-1;i>0;i--){ const j=Math.floor(Math.random()*(i+1)); [a[i],a[j]]=[a[j],a[i]]; }
  return a;
}
function dist(a,b){ return Math.hypot(a.x-b.x, a.z-b.z); }

let map = null;
let three = { renderer:null, scene:null, camera:null, killerMesh:null, locMeshes:[], exitMesh:null, altarMesh:null, flashLight:null, flashTarget:null, container:null, raycaster:null, plane:null };

function startSearchPhase(){
  const contents = shuffle(['head','arm','leg','axe','empty','empty','empty','empty']);
  map = {
    locations: LOCATIONS_LAYOUT.map((l,idx)=>({...l, type:contents[idx], visited:false})),
    player: { x:PLAYER_START.x, z:PLAYER_START.z, heading: Math.PI },
    killer: { x:KILLER_START.x, z:KILLER_START.z, active:false },
    keys: {}, moveTarget:null,
    searching:false, searchTimer:0, searchTarget:null,
    visionRadius: 130,
    killerWasVisible:false, elapsed:0, lastFootstep:0,
    canLeave:false, frozen:false,
    jumpscareCooldown: 8
  };
  state.partsFound = []; state.hasAxe = false; state.timeLeft = 200;
  state.lastLog = "Somewhere out there, he's already moving.";
  screenSearch();
  initScene();
  bind3DInput();
  startHeartbeat(1500);
  window.addEventListener('keydown', onKeyDown);
  window.addEventListener('keyup', onKeyUp);
  requestAnimationFrame(gameTick);
}

function onKeyDown(e){
  if(state.screen !== 'search') return;
  map.keys[e.key.toLowerCase()] = true;
  if(e.key.toLowerCase()==='e') attemptSearch();
}
function onKeyUp(e){ if(map) map.keys[e.key.toLowerCase()] = false; }

function screenSearch(){
  state.screen = 'search';
  const partsInv = PARTS_NEEDED.map(p=>{
    const got = state.partsFound.includes(p);
    return `<div class="inv-item ${got?'got':''}">${p}${got?' ✓':''}</div>`;
  }).join('');
  const axeInv = `<div class="inv-item ${state.hasAxe?'got':''}">axe${state.hasAxe?' ✓':''}</div>`;
  render(`
    <div class="hud">
      <div>Time until nightfall<br><span class="time" id="timeLabel">${fmtTime(state.timeLeft)}</span></div>
      <div class="danger-label" id="dangerLabel">It's quiet. For now.</div>
    </div>
    <div class="inventory">${partsInv}${axeInv}</div>
    <div class="map-wrap"><div id="mapContainer"></div></div>
    <div class="nearby" id="nearby"></div>
    <div class="search-btn-wrap">
      <button class="search-btn" id="searchBtn" disabled onclick="attemptSearch()">Search</button>
    </div>
    <div class="log" id="log">${state.lastLog}</div>
    <div class="controls-hint">W/S walk forward/back, A/D or arrows to turn. Tap the ground to walk there.<br>
    E or Search when close. Once you have everything: run HOME, or return to the altar.</div>
  `);
}

function fmtTime(s){ const m=Math.floor(s/60), sec=Math.max(0,Math.ceil(s%60)); return m+":"+(sec<10?'0':'')+sec; }

/* ---------------- Three.js scene (first person) ---------------- */
function initScene(){
  const container = document.getElementById('mapContainer');
  const w = container.clientWidth || MAP_W;
  const h = Math.round(w * (MAP_H/MAP_W));
  container.style.height = h+'px';

  const fogColor = 0x07080a;
  const scene = new THREE.Scene();
  scene.background = new THREE.Color(fogColor);
  scene.fog = new THREE.FogExp2(fogColor, 0.0115);

  const camera = new THREE.PerspectiveCamera(70, w/h, 0.1, 2000);

  const renderer = new THREE.WebGLRenderer({antialias:true});
  renderer.setSize(w,h);
  renderer.setPixelRatio(Math.min(window.devicePixelRatio||1, 1.5));
  container.appendChild(renderer.domElement);

  scene.add(new THREE.AmbientLight(0x15170e, 0.55));

  const flashLight = new THREE.SpotLight(0xd8b878, 2.0, 300, Math.PI/6, 0.4, 1.6);
  const flashTarget = new THREE.Object3D();
  scene.add(flashTarget);
  flashLight.target = flashTarget;
  scene.add(flashLight);

  const ground = new THREE.Mesh(
    new THREE.PlaneGeometry(1400,1400),
    new THREE.MeshStandardMaterial({color:0x0b0d07})
  );
  ground.rotation.x = -Math.PI/2;
  scene.add(ground);

  for(let i=0;i<70;i++){
    const tx = (Math.random()-0.5)*1100;
    const tz = (Math.random()-0.5)*1100;
    if(Math.hypot(tx,tz) < 45) continue;
    const trunk = new THREE.Mesh(
      new THREE.CylinderGeometry(2.5,3.5,18,6),
      new THREE.MeshStandardMaterial({color:0x14100a})
    );
    trunk.position.set(tx,9,tz);
    const foliage = new THREE.Mesh(
      new THREE.ConeGeometry(12,34,7),
      new THREE.MeshStandardMaterial({color:0x0e1509})
    );
    foliage.position.set(tx,30,tz);
    scene.add(trunk); scene.add(foliage);
  }

  // killer body
  const killerMesh = new THREE.Group();
  const kBody = new THREE.Mesh(
    new THREE.CylinderGeometry(5,5,18,8),
    new THREE.MeshStandardMaterial({color:0x1c1c18, flatShading:true})
  );
  kBody.position.y = 9;

  // low-poly faceted head (the mother's mask)
  const kHead = new THREE.Group();
  const kSkull = new THREE.Mesh(
    new THREE.IcosahedronGeometry(5.6, 0),
    new THREE.MeshStandardMaterial({color:0xcdc2a0, flatShading:true, emissive:0x3a1010, emissiveIntensity:0.3})
  );
  const kEyeMat = new THREE.MeshStandardMaterial({color:0x050302});
  const kEyeL = new THREE.Mesh(new THREE.SphereGeometry(0.9,6,6), kEyeMat);
  kEyeL.position.set(-2.0, 0.6, 4.6);
  const kEyeR = kEyeL.clone(); kEyeR.position.x = 2.0;
  const kMouth = new THREE.Mesh(new THREE.BoxGeometry(3,0.6,0.4), kEyeMat);
  kMouth.position.set(0, -2.6, 4.9);
  kHead.add(kSkull, kEyeL, kEyeR, kMouth);
  kHead.position.y = 24;

  killerMesh.add(kBody, kHead);
  killerMesh.visible = false;
  scene.add(killerMesh);

  const locMeshes = map.locations.map(loc=>{
    const m = new THREE.Mesh(
      new THREE.SphereGeometry(3.6,8,8),
      new THREE.MeshStandardMaterial({color:0x8a7020, emissive:0x8a7020, emissiveIntensity:0.9})
    );
    m.position.set(loc.x, 8, loc.z);
    m.visible = false;
    scene.add(m);
    return m;
  });

  const exitMesh = new THREE.Mesh(
    new THREE.CylinderGeometry(10,10,1,16),
    new THREE.MeshStandardMaterial({color:0x2f5a2f, emissive:0x4c8c4c, emissiveIntensity:0.7})
  );
  exitMesh.position.set(EXIT_POS.x, 0.5, EXIT_POS.z);
  exitMesh.visible = false;
  scene.add(exitMesh);

  const altarMesh = new THREE.Mesh(
    new THREE.CylinderGeometry(10,10,1,16),
    new THREE.MeshStandardMaterial({color:0x3a2a5a, emissive:0x6a4c9c, emissiveIntensity:0.7})
  );
  altarMesh.position.set(ALTAR_POS.x, 0.5, ALTAR_POS.z);
  altarMesh.visible = false;
  scene.add(altarMesh);

  three = {
    renderer, scene, camera, killerMesh, locMeshes, exitMesh, altarMesh,
    flashLight, flashTarget, container, raycaster: new THREE.Raycaster(), plane: new THREE.Plane(new THREE.Vector3(0,1,0), 0)
  };
}

function on3DResize(){
  if(!three.renderer || !three.container) return;
  const w = three.container.clientWidth;
  const h = Math.round(w * (MAP_H/MAP_W));
  three.container.style.height = h+'px';
  three.renderer.setSize(w,h);
  three.camera.aspect = w/h;
  three.camera.updateProjectionMatrix();
}
window.addEventListener('resize', on3DResize);

function bind3DInput(){
  const dom = three.renderer.domElement;
  const setTargetFromEvent = (clientX, clientY)=>{
    const rect = dom.getBoundingClientRect();
    const nx = ((clientX-rect.left)/rect.width)*2-1;
    const ny = -((clientY-rect.top)/rect.height)*2+1;
    three.raycaster.setFromCamera({x:nx,y:ny}, three.camera);
    const pt = new THREE.Vector3();
    three.raycaster.ray.intersectPlane(three.plane, pt);
    if(pt) map.moveTarget = { x:pt.x, z:pt.z };
  };
  dom.addEventListener('mousedown', e=> setTargetFromEvent(e.clientX,e.clientY));
  dom.addEventListener('touchstart', e=>{
    const t = e.touches[0]; setTargetFromEvent(t.clientX,t.clientY); e.preventDefault();
  }, {passive:false});
}

/* ---------------- game loop ---------------- */
let lastTs = null;
function gameTick(ts){
  if(state.screen !== 'search'){ lastTs=null; return; }
  if(lastTs===null) lastTs = ts;
  let dt = (ts - lastTs)/1000; lastTs = ts;
  dt = Math.min(dt, 0.05);

  if(!map.searching && !map.frozen) updatePlayer(dt);
  updateKiller(dt);
  map.elapsed += dt;

  state.timeLeft -= dt;
  if(state.timeLeft <= 0){ state.timeLeft = 0; endSearchPhase('time'); return; }

  const d = map.killer.active ? dist(map.player, map.killer) : 9999;
  handleDanger(d, dt);
  handleRandomJumpscares(dt);

  if(map.killer.active && d < 20){ endSearchPhase('caught'); return; }

  if(map.searching){
    map.searchTimer -= dt;
    if(map.searchTimer <= 0) resolveSearch();
  }

  renderScene(d);
  updateHudLive(d);

  state.rafHandle = requestAnimationFrame(gameTick);
}

function updatePlayer(dt){
  const turnSpeed = 2.6;
  const moveSpeed = 130;
  let turn=0, move=0;
  if(map.keys['a']||map.keys['arrowleft']) turn += 1;
  if(map.keys['d']||map.keys['arrowright']) turn -= 1;
  if(map.keys['w']||map.keys['arrowup']) move += 1;
  if(map.keys['s']||map.keys['arrowdown']) move -= 1;

  if(turn !== 0){
    map.player.heading += turn*turnSpeed*dt;
    map.moveTarget = null;
  }
  if(move !== 0){
    map.moveTarget = null;
    map.player.x += Math.sin(map.player.heading)*move*moveSpeed*dt;
    map.player.z += Math.cos(map.player.heading)*move*moveSpeed*dt;
  } else if(map.moveTarget){
    const d = dist(map.player, map.moveTarget);
    if(d > 4){
      const tx = map.moveTarget.x - map.player.x;
      const tz = map.moveTarget.z - map.player.z;
      const targetHeading = Math.atan2(tx, tz);
      let diff = targetHeading - map.player.heading;
      diff = Math.atan2(Math.sin(diff), Math.cos(diff));
      map.player.heading += diff * Math.min(1, dt*6);
      map.player.x += Math.sin(map.player.heading)*moveSpeed*dt;
      map.player.z += Math.cos(map.player.heading)*moveSpeed*dt;
    } else { map.moveTarget = null; }
  }
  map.player.x = Math.max(-260, Math.min(260, map.player.x));
  map.player.z = Math.max(-160, Math.min(160, map.player.z));
}

function updateKiller(dt){
  if(!map.killer.active){ if(map.elapsed > 7) map.killer.active = true; return; }
  const urgency = 1 - (state.timeLeft/200);
  // slower base pace, still ramps up as nightfall approaches
  const baseSpeed = 40 + urgency*42 + Math.min(map.elapsed,60)*0.3;
  const dx = map.player.x - map.killer.x;
  const dz = map.player.z - map.killer.z;
  const d = Math.hypot(dx,dz) || 1;
  const wob = Math.sin(map.elapsed*3) * 18;
  const nx = dx/d, nz = dz/d;
  map.killer.x += (nx*baseSpeed + -nz*wob*0.02) * dt;
  map.killer.z += (nz*baseSpeed + nx*wob*0.02) * dt;
}

function handleDanger(d, dt){
  const near = Math.max(0, Math.min(1, 1 - d/300));
  setVignette(near*0.6);
  setHeartbeatRate(1500 - near*1150);

  const visibleNow = map.killer.active && d <= map.visionRadius;
  if(visibleNow && !map.killerWasVisible){
    triggerJumpscare();
    map.jumpscareCooldown = 9;
  }
  map.killerWasVisible = visibleNow;

  map.lastFootstep = (map.lastFootstep||0) + dt;
  if(map.killer.active && d < 220 && map.lastFootstep > 0.8){
    map.lastFootstep = 0;
    playFootstepBurst(0.28*(1-d/220));
  }

  const mapWrap = document.querySelector('.map-wrap');
  if(mapWrap && !map.searching){
    if(d < 90){
      const s = (Math.random()-0.5)*6;
      mapWrap.style.transform = `translate(${s}px, ${(Math.random()-0.5)*6}px)`;
    } else { mapWrap.style.transform = 'none'; }
  }
}

function handleRandomJumpscares(dt){
  map.jumpscareCooldown -= dt;
  if(map.jumpscareCooldown <= 0){
    if(Math.random() < dt*0.045){
      triggerJumpscare();
      map.jumpscareCooldown = 12 + Math.random()*12;
    }
  }
}

function attemptSearch(){
  if(map.searching || map.frozen) return;
  const loc = map.locations.find(l=> !l.visited && dist(map.player,l) < 34);
  if(!loc) return;
  map.searching = true; map.searchTarget = loc; map.searchTimer = 1.3;
  const logEl = document.getElementById('log');
  if(logEl) logEl.textContent = `Searching ${loc.name}...`;
}

function resolveSearch(){
  const loc = map.searchTarget;
  loc.visited = true; map.searching = false;
  let log = "";
  if(PARTS_NEEDED.includes(loc.type)){
    state.partsFound.push(loc.type);
    log = `You find Robert's ${loc.type} at ${loc.name}. Your hands are shaking as you take it.`;
    playTone(320,0.4,'sine',0.15);
    if(state.partsFound.length === PARTS_NEEDED.length){
      map.canLeave = true;
      log += " That's everything.";
    }
  } else if(loc.type === 'axe'){
    state.hasAxe = true;
    log = `Something heavy is wedged in the ground at ${loc.name} — Barry's lost axe. You take it.`;
    playTone(200,0.35,'square',0.12);
  } else {
    log = SCARES[Math.floor(Math.random()*SCARES.length)];
    playNoiseBurst(0.25,0.12);
    if(Math.random() < 0.4){ triggerJumpscare(); map.jumpscareCooldown = 10; }
  }
  state.lastLog = log;
  const logEl = document.getElementById('log');
  if(logEl) logEl.textContent = log;
  refreshInventoryHud();
}

function refreshInventoryHud(){
  const inv = document.querySelector('.inventory');
  if(!inv) return;
  const partsInv = PARTS_NEEDED.map(p=>{
    const got = state.partsFound.includes(p);
    return `<div class="inv-item ${got?'got':''}">${p}${got?' ✓':''}</div>`;
  }).join('');
  const axeInv = `<div class="inv-item ${state.hasAxe?'got':''}">axe${state.hasAxe?' ✓':''}</div>`;
  inv.innerHTML = partsInv + axeInv;
}

function updateHudLive(d){
  const timeLabel = document.getElementById('timeLabel');
  if(timeLabel) timeLabel.textContent = fmtTime(state.timeLeft);

  const dangerLabel = document.getElementById('dangerLabel');
  if(dangerLabel){
    let txt = "It's quiet. For now.";
    if(map.killer.active){
      if(d < 60) txt = "HE'S RIGHT THERE. RUN.";
      else if(d < 140) txt = "You can hear him breathing.";
      else if(d < 260) txt = "Footsteps, somewhere behind you.";
    }
    dangerLabel.textContent = txt;
  }

  const nearLoc = map.locations.find(l=>!l.visited && dist(map.player,l) < map.visionRadius);
  const nearbyEl = document.getElementById('nearby');
  if(nearbyEl){
    if(map.canLeave && dist(map.player, EXIT_POS) < map.visionRadius) nearbyEl.textContent = "Nearby: the way HOME";
    else if(map.canLeave && dist(map.player, ALTAR_POS) < map.visionRadius) nearbyEl.textContent = "Nearby: the altar";
    else nearbyEl.textContent = nearLoc ? `Nearby: ${nearLoc.name}` : "";
  }

  const searchBtn = document.getElementById('searchBtn');
  if(searchBtn){
    const inRange = !map.searching && map.locations.some(l=>!l.visited && dist(map.player,l) < 34);
    searchBtn.disabled = !inRange && !map.searching;
    searchBtn.textContent = map.searching ? 'Searching...' : 'Search';
  }

  if(map.canLeave){
    if(dist(map.player, EXIT_POS) < 26){ finishLeaveForest(); return; }
    if(dist(map.player, ALTAR_POS) < 26){ finishOffering(); return; }
  }
}

function renderScene(dToKiller){
  if(!three.renderer) return;

  three.camera.position.set(map.player.x, EYE_HEIGHT, map.player.z);
  const lookX = map.player.x + Math.sin(map.player.heading)*10;
  const lookZ = map.player.z + Math.cos(map.player.heading)*10;
  three.camera.lookAt(lookX, EYE_HEIGHT, lookZ);

  three.flashLight.position.set(map.player.x, EYE_HEIGHT+1, map.player.z);
  three.flashTarget.position.set(lookX, EYE_HEIGHT-3, lookZ);

  three.killerMesh.position.set(map.killer.x, 0, map.killer.z);
  three.killerMesh.visible = map.killer.active;

  map.locations.forEach((loc,idx)=>{
    const mesh = three.locMeshes[idx];
    const d = dist(map.player, loc);
    mesh.visible = d < map.visionRadius || loc.visited;
    if(loc.visited){ mesh.material.color.set(0x2c2c22); mesh.material.emissiveIntensity = 0.1; }
  });

  three.exitMesh.visible = map.canLeave;
  three.altarMesh.visible = map.canLeave;

  three.renderer.render(three.scene, three.camera);
}

function endSearchPhase(reason){
  cancelAnimationFrame(state.rafHandle);
  state.screen = 'ended-search';
  window.removeEventListener('keydown', onKeyDown);
  window.removeEventListener('keyup', onKeyUp);
  stopHeartbeat();
  setVignette(0);
  playStinger();
  if(state.hasAxe) screenFightSequence();
  else screenEnding3();
}

function finishLeaveForest(){
  cancelAnimationFrame(state.rafHandle);
  state.screen = 'left';
  window.removeEventListener('keydown', onKeyDown);
  window.removeEventListener('keyup', onKeyUp);
  stopHeartbeat();
  setVignette(0);
  render(`
    <h2 class="heading">Out of the Trees</h2>
    <p class="body-text">You break through the treeline with Robert's remains gathered against your chest.
    Behind you, the forest goes quiet — too quiet. Your house lights are still on.</p>
    <button class="primary" onclick="screenPostRun()">Go inside</button>
  `);
}

function finishOffering(){
  cancelAnimationFrame(state.rafHandle);
  state.screen = 'left';
  window.removeEventListener('keydown', onKeyDown);
  window.removeEventListener('keyup', onKeyUp);
  stopHeartbeat();
  setVignette(0);
  screenEnding5();
}

function screenPostRun(){
  render(`
    <h2 class="heading">What Now</h2>
    <p class="body-text">You're standing in your own kitchen. Robert is on the table in front of you. You have to decide what happens next.</p>
    <div class="btn-row">
      <button class="choice" onclick="screenEnding1()">Tell the cops</button>
      <button class="choice" onclick="screenEnding2()">Keep it to yourself</button>
    </div>
  `);
}

/* =========================================================
   FIGHT SEQUENCE (Ending 4 path)
========================================================= */
function screenFightSequence(){
  render(`
    <h2 class="heading">He Catches You</h2>
    <p class="body-text">He's fast, faster than anything wearing a dead woman's head has any right to be. But your hand
    closes around the axe first.</p>
    <p class="body-text">The fight is short and it is ugly. When it's over, you're the one still standing.</p>
    <button class="primary" onclick="screenMaskReveal()">Take the mask</button>
  `);
}
function screenMaskReveal(){
  render(`
    <p class="body-text">You pull the head away. Underneath it is a face you know. It's Robert's father.</p>
    <p class="body-text dim">You don't stay to understand why. You run.</p>
    <button class="primary" onclick="screenEnding4()">Run home</button>
  `);
}

/* =========================================================
   ENDINGS
========================================================= */
function endingWrap(title, text, note){
  render(`
    <h2 class="heading flicker">${title}</h2>
    <p class="body-text">${text}</p>
    ${note ? `<p class="dim body-text">${note}</p>` : ''}
    <button class="primary" onclick="screenTitle()">Play again</button>
  `);
}
function screenEnding1(){
  endingWrap("ENDING — THE FAILED HERO",
    `You call it in and tell the police everything — the quiz, the message, the man in the mask, all of it.
    You know too much, in too much detail, about a murder that happened hours ago in a forest with no
    other witnesses. They stop taking notes and start reading you your rights.
    You spend the rest of your life in a locked ward, still insisting you were telling the truth.`);
}
function screenEnding2(){
  endingWrap("ENDING — INSANITY",
    `You say nothing. When Robert's body is found, the report reads "animal attack." Case closed.
    You carry the truth alone, and it does not get lighter with time. Months later, unable to hold
    the weight of what you saw — and what you didn't say — you take your own life.`,
    "If you're going through something heavy right now, you don't have to carry it alone — it's worth talking to someone you trust or a crisis line in your area.");
}
function screenEnding3(){
  endingWrap("ENDING — THE VICTIM",
    `You don't make it out in time. He catches you, and what happens next mirrors exactly
    what happened to Robert. In the morning, a search party finds you both. The report reads: animal attack.
    Case closed.`);
}
function screenEnding4(){
  endingWrap("ENDING — THE HERO",
    `You make it home and call the police before you even catch your breath. This time, there's a body to
    find, a weapon, a mask, a face. They believe you. The following day, the town holds Robert's funeral —
    and for the first time since this started, you let yourself grieve properly.`);
}
function screenEnding5(){
  endingWrap("ENDING — THE OFFERING",
    `Instead of running, you go back to where this started. You kneel in the dirt and arrange Robert's
    remains exactly the way you were told to. The man doesn't move and doesn't speak until you're finished.
    Then he sets the mask down at your feet and walks into the trees, and doesn't come back.
    You go home that night unharmed. But you keep the mask in a box in your closet, and some nights
    you swear you can hear something asking you to put it on.`);
}

/* INIT */
screenTitle();
</script>
</body>
</html>
