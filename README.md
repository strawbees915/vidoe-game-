[voxel-hunting-game-main.html](https://github.com/user-attachments/files/33129290/voxel-hunting-game-main.html)
# vidoe-game-
a video game about hunting 
<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Timber Isle — Isometric Voxel Hunting</title>
<style>
  *{margin:0;padding:0;box-sizing:border-box}
  html,body{width:100%;height:100%;overflow:hidden;background:#101826;font-family:'Segoe UI',system-ui,sans-serif}
  #app{position:fixed;inset:0}
  canvas{display:block}
  #hud{position:fixed;inset:0;pointer-events:none;z-index:10}
  #topbar{position:absolute;top:14px;left:16px;right:16px;display:flex;justify-content:space-between;align-items:flex-start}
  .panel{background:rgba(20,28,18,.72);backdrop-filter:blur(6px);border:1px solid rgba(255,255,255,.15);border-radius:12px;color:#fff;padding:10px 14px;min-width:170px}
  .panel h3{font-size:10px;letter-spacing:3px;opacity:.6;font-weight:600;margin-bottom:4px}
  .panel .big{font-size:22px;font-weight:800;letter-spacing:1px}
  .panel .sub{font-size:11px;opacity:.75;margin-top:2px}
  #ammo{display:flex;gap:5px;margin-top:6px}
  .bullet{width:8px;height:18px;border-radius:4px;background:#ffcf4d;border:1px solid rgba(0,0,0,.4)}
  .bullet.empty{background:rgba(255,255,255,.15);border-color:rgba(255,255,255,.2)}
  #cross{position:absolute;top:50%;left:50%;transform:translate(-50%,-50%);display:none}
  #fpscross{position:absolute;top:50%;left:50%;width:24px;height:24px;transform:translate(-50%,-50%);display:none;pointer-events:none}
  #fpscross:before,#fpscross:after{content:'';position:absolute;background:rgba(255,255,255,.95);box-shadow:0 1px 3px rgba(0,0,0,.6)}
  #fpscross:before{left:11px;top:2px;width:2px;height:20px}
  #fpscross:after{top:11px;left:2px;width:20px;height:2px}
  #viewbtn{position:absolute;bottom:14px;left:50%;transform:translateX(-50%);pointer-events:auto;cursor:pointer;background:rgba(20,28,18,.75);border:1px solid rgba(255,223,138,.5);color:#ffdf8a;font-weight:800;font-size:12px;letter-spacing:2px;padding:10px 18px;border-radius:24px}
  #viewbtn:hover{background:rgba(61,106,42,.9);color:#fff}
  #toast{position:absolute;top:88px;left:50%;transform:translateX(-50%);display:flex;flex-direction:column;gap:8px;align-items:center}
  .tmsg{background:rgba(20,30,15,.85);color:#ffe9a8;padding:8px 16px;border-radius:20px;font-size:13px;font-weight:700;letter-spacing:1px;animation:tin 3s forwards;border:1px solid rgba(255,233,168,.3)}
  @keyframes tin{0%{opacity:0;transform:translateY(10px)}10%{opacity:1;transform:translateY(0)}80%{opacity:1}100%{opacity:0;transform:translateY(-10px)}}
  #help{position:absolute;bottom:14px;left:16px;color:#fff;font-size:11.5px;line-height:1.8;background:rgba(20,28,18,.6);padding:10px 14px;border-radius:10px;letter-spacing:.3px}
  #help b{color:#ffdf8a}
  #wave{position:absolute;bottom:14px;right:16px;text-align:right}
  #start{position:fixed;inset:0;z-index:50;background:radial-gradient(ellipse at center,rgba(60,30,30,.55),rgba(15,20,12,.92));display:flex;align-items:center;justify-content:center;cursor:pointer}
  #card{max-width:560px;background:#f5efe0;border-radius:18px;overflow:hidden;box-shadow:0 30px 80px rgba(0,0,0,.5);margin:16px}
  #card .art{height:210px;background:linear-gradient(180deg,#7ec8e3 0%,#b8e0c8 55%,#5a7a3a 55%,#3d5228 100%);position:relative;overflow:hidden}
  #card .art:after{content:'🏕️🌲🦌🐗🐇';position:absolute;bottom:18px;left:0;right:0;text-align:center;font-size:52px;filter:drop-shadow(0 6px 0 rgba(0,0,0,.2))}
  #card .body{padding:26px 28px}
  #card h1{font-size:30px;letter-spacing:2px;color:#2a3a1e}
  #card h1 span{color:#b06a2a}
  #card p{font-size:13.5px;color:#5a6a4a;margin:10px 0 16px;line-height:1.6}
  #card .keys{display:grid;grid-template-columns:1fr 1fr;gap:8px;font-size:12px;color:#333}
  #card .keys div{background:#e9e2cf;padding:8px 10px;border-radius:8px}
  #playbtn{margin-top:18px;background:#3d6a2a;color:#fff;text-align:center;padding:14px;border-radius:12px;font-weight:800;letter-spacing:3px;font-size:14px}
  #hitmark{position:absolute;top:50%;left:50%;width:40px;height:40px;transform:translate(-50%,-50%);pointer-events:none;display:none}
  #hitmark:before,#hitmark:after{content:'';position:absolute;background:#ff3b3b}
  #hitmark:before{left:19px;top:0;width:2px;height:40px}
  #hitmark:after{top:19px;left:0;width:40px;height:2px}
  #xpbar{height:5px;background:rgba(255,255,255,.15);border-radius:3px;overflow:hidden;margin-top:6px}
  #xpfill{height:100%;width:0%;background:linear-gradient(90deg,#ffdf8a,#7ddb52);border-radius:3px;transition:width .4s}
  #hpbar{height:6px;background:rgba(0,0,0,.35);border-radius:3px;overflow:hidden;margin-top:6px}
  #hpfill{height:100%;width:100%;background:linear-gradient(90deg,#e05050,#ff8a5a);border-radius:3px;transition:width .2s}
  #hpfill.low{animation:hpulse 1s infinite}
  @keyframes hpulse{0%,100%{opacity:1}50%{opacity:.5}}
  #dmg{position:fixed;inset:0;pointer-events:none;z-index:30;opacity:0;background:radial-gradient(ellipse at center,transparent 40%,rgba(200,0,0,.65) 100%);transition:opacity .1s}
  #flash{position:fixed;inset:0;pointer-events:none;z-index:29;opacity:0;background:#e8f0ff}
  #scopevig{position:fixed;inset:0;pointer-events:none;z-index:25;opacity:0;background:radial-gradient(ellipse at center,transparent 42%,rgba(0,0,0,.78) 78%);transition:opacity .15s}
  #biteprompt{position:absolute;top:34%;left:50%;transform:translateX(-50%);background:rgba(20,30,15,.92);border:2px solid #ffdf8a;color:#ffdf8a;padding:12px 26px;border-radius:28px;font-size:20px;font-weight:800;letter-spacing:2px;display:none;pointer-events:none;animation:bitepulse .5s infinite}
  @keyframes bitepulse{0%,100%{transform:translateX(-50%) scale(1)}50%{transform:translateX(-50%) scale(1.08)}}
  #buildbar{position:absolute;bottom:64px;left:50%;transform:translateX(-50%);display:none;gap:8px;pointer-events:auto}
  #buildbar .bopt{background:rgba(20,28,18,.85);border:2px solid rgba(255,255,255,.2);color:#fff;border-radius:10px;padding:8px 14px;font-size:12px;font-weight:700;cursor:pointer;text-align:center;letter-spacing:.5px}
  #buildbar .bopt.sel{border-color:#ffdf8a;color:#ffdf8a}
  #buildbar .bopt.cant{opacity:.45}
  #buildbar .bopt small{display:block;font-size:10px;opacity:.7;font-weight:400}
  #minimapwrap{position:absolute;top:176px;right:16px;width:150px;height:150px;border-radius:50%;overflow:hidden;border:2px solid rgba(255,255,255,.35);box-shadow:0 4px 16px rgba(0,0,0,.35);background:#223a4a}
  #minimap{width:100%;height:100%}
  #compasswrap{margin-top:6px;background:rgba(0,0,0,.25);border-radius:8px;padding:2px 6px}
  #compass{display:block;width:100%;height:30px}
  #topbar .panel h3{white-space:nowrap;overflow:hidden;text-overflow:ellipsis;max-width:220px}
  #ammo .bullet{width:7px;height:15px}
  @media (max-width:900px){
    .panel{min-width:120px;padding:8px 10px}
    .panel .big{font-size:16px}
    #minimapwrap{width:110px;height:110px;top:160px}
    #mission{display:none}
    #help{display:none}
  }
  #gameover{position:fixed;inset:0;z-index:60;display:none;align-items:center;justify-content:center;background:radial-gradient(ellipse at center,rgba(80,0,0,.55),rgba(10,5,5,.92))}
  #gocard{width:420px;max-width:92vw;background:#f5efe0;border-radius:16px;overflow:hidden;box-shadow:0 30px 80px rgba(0,0,0,.6);text-align:center}
  #gocard .head{background:#5a1414;color:#ffd0d0;padding:20px;font-size:26px;font-weight:800;letter-spacing:3px}
  #gocard .body{padding:22px 26px;color:#333;font-size:14px;line-height:1.7}
  #gocard .stats{font-size:13px;color:#5a6a4a;margin:10px 0}
  #respawnbtn{margin-top:14px;background:#3d6a2a;color:#fff;padding:14px;border-radius:12px;font-weight:800;letter-spacing:3px;font-size:14px;cursor:pointer}
  #mission{position:absolute;bottom:86px;left:16px;max-width:280px;background:rgba(20,28,18,.72);border:1px solid rgba(255,223,138,.35);border-radius:10px;color:#ffe9a8;padding:10px 14px;font-size:12px;line-height:1.5;letter-spacing:.3px}
  #mission b{color:#fff;letter-spacing:1px}
  #mission .done{color:#7ddb52}
  #shopprompt{position:absolute;bottom:150px;left:50%;transform:translateX(-50%);background:rgba(20,30,15,.9);border:1px solid rgba(255,223,138,.5);color:#ffdf8a;padding:10px 20px;border-radius:24px;font-size:13px;font-weight:800;letter-spacing:1px;display:none;pointer-events:none}
  #shop{position:fixed;inset:0;z-index:40;display:none;align-items:center;justify-content:center;background:rgba(10,14,8,.7)}
  #shopcard{width:420px;max-width:92vw;background:#f5efe0;border-radius:16px;overflow:hidden;box-shadow:0 30px 80px rgba(0,0,0,.6)}
  #shopcard .head{background:#2a3a1e;color:#ffdf8a;padding:16px 20px;font-weight:800;letter-spacing:2px;font-size:14px;display:flex;justify-content:space-between;align-items:center}
  #shopcard .head button{background:none;border:none;color:#fff;font-size:18px;cursor:pointer}
  #shopcard .body{padding:16px 20px;color:#333;font-size:13px;max-height:60vh;overflow:auto}
  #shopcard .row{display:flex;justify-content:space-between;align-items:center;background:#e9e2cf;border-radius:10px;padding:10px 12px;margin-bottom:8px}
  #shopcard .row .t b{display:block;font-size:13px}
  #shopcard .row .t span{font-size:11px;color:#6a7a5a}
  #shopcard button.buy{background:#3d6a2a;color:#fff;border:none;border-radius:8px;padding:8px 14px;font-weight:800;cursor:pointer;letter-spacing:1px}
  #shopcard button.buy:disabled{background:#999;cursor:default}
  #shopcard button.eq{background:#b06a2a}
  #shopbal{font-size:12px;color:#5a6a4a;margin-bottom:10px}
  #shopbal b{color:#b06a2a}
</style>
<script type="importmap">
{ "imports": { "three": "https://unpkg.com/three@0.160.0/build/three.module.js" } }
</script>
</head>
<body>
<div id="app"></div>
<div id="hud">
  <div id="topbar">
    <div class="panel"><h3>SCORE • <span id="coins" style="color:#ffdf8a">🪙 0</span></h3><div class="big" id="score">0</div><div class="sub" id="kills">0 kills • 0 meat</div><div id="hpbar"><div id="hpfill"></div></div></div>
    <div class="panel" style="text-align:center"><h3>TIMBER ISLE • LV <span id="level">1</span></h3><div class="sub" id="objective">Hunt 8 animals • V toggles 1st/iso • Q/E rotate</div><div class="sub" id="clock">☀️ Day 1 08:00</div><div id="compasswrap"><canvas id="compass" width="260" height="30"></canvas></div><div id="xpbar"><div id="xpfill"></div></div></div>
    <div class="panel"><h3><span id="wname">RUSTY RIFLE</span> • <span id="ammotxt">6 / ∞</span></h3><div id="ammo"></div><div class="sub">1/2/3 tools • Click use • R reload • F use</div></div>
  </div>
  <div id="toast"></div>
  <div id="minimapwrap"><canvas id="minimap" width="150" height="150"></canvas></div>
  <div id="mission"><b>🎯 MISSION</b><div id="missiontxt">…</div></div>
  <div id="shopprompt">🏠 Press <b>F</b> to trade</div>
  <div id="help"><b>WASD</b> move/paddle • <b>Click</b> use • <b>G</b> eat • <b>1/2/3</b> tools<br><b>V</b> view • <b>R</b> reload • <b>C</b> scout • <b>B</b> build • <b>F</b> use/boat • <b>Q/E</b> rotate • 🐺 beware</div>
  <div id="wave" class="panel"><h3>WAVE</h3><div class="big" id="wavenum">1</div><div class="sub" id="left">8 left</div></div>
  <div id="hitmark"></div>
  <div id="fpscross"></div>
  <div id="biteprompt">❗ STRIKE — CLICK! ❗</div>
  <div id="buildbar"></div>
  <button id="viewbtn">🎥 VIEW: ISOMETRIC [V]</button>
</div>
<div id="dmg"></div>
<div id="flash"></div>
<div id="scopevig"></div>
<div id="gameover"><div id="gocard">
  <div class="head">💀 MAULED</div>
  <div class="body">
    The wolves got you.
    <div class="stats" id="gostats"></div>
    <div style="font-size:12px;color:#6a7a5a">You keep XP, levels, coins & rifles. -10% coins scavenger fee.</div>
    <div id="respawnbtn">▶ RESPAWN AT MEADOW</div>
  </div>
</div></div>
<div id="shop"><div id="shopcard">
  <div class="head"><span>🏠 CABIN TRADER</span><button id="shopx">✕</button></div>
  <div class="body">
    <div id="shopbal"></div>
    <div id="shoprows"></div>
    <div style="font-size:11px;color:#6a7a5a">Meat sells for 6🪙 each • Ammo refill 10🪙 • Progress autosaves</div>
    <div style="margin-top:8px"><button class="buy" id="resetsave" style="background:#8a2f2f">Reset progress</button></div>
  </div>
</div></div>
<div id="start">
  <div id="card">
    <div class="art"></div>
    <div class="body">
      <h1>TIMBER ISLE <span>• VOXEL HUNT</span></h1>
      <p>Big 96×96 terrain with a 4-minute <b>day/night cycle</b>: torches, campfire and cabin glow after dark — and <b>wolves get bolder at night</b>. Earn <b>XP</b>, finish <b>missions</b>, sell meat at the <b>cabin trader (E)</b>, upgrade rifles. Eat meat (<b>F</b>) to heal. Don't bleed out.</p>
      <div class="keys">
        <div>🕹️ <b>WASD</b> — move (W = gun fwd)</div>
        <div>🎯 <b>Mouse + Click</b> — aim & shoot</div>
        <div>🔄 <b>V</b> — 1st/iso • <b>Q/E</b> — rotate</div>
        <div>🏠 <b>F</b> — trade/sit • <b>G</b> — eat • <b>R</b> — reload</div>
        <div>🎣 <b>1/2</b> — rifle/rod • sit on beach chairs</div>
        <div>🔭 <b>C</b> — scout zoom • cast at blue water</div>
        <div>⛏️ <b>3</b> — axe • chop trees for 🪵</div>
        <div>🔨 <b>B</b> — build walls, spikes, torches</div>
        <div>🚣 <b>F</b> — board the skiff • paddle WASD • fish aboard</div>
        <div>🐺 <b>Wolves</b> — stalk from wave 2 • <b>G</b> eats to heal</div>
      </div>
      <div id="playbtn">▶ CLICK TO START HUNTING</div>
    </div>
  </div>
</div>

<script type="module">
import * as THREE from 'three';

// ---------- renderer / scene ----------
const app = document.getElementById('app');
const renderer = new THREE.WebGLRenderer({antialias:true});
renderer.setSize(innerWidth, innerHeight);
renderer.setPixelRatio(Math.min(devicePixelRatio,2));
renderer.shadowMap.enabled = true;
renderer.shadowMap.type = THREE.PCFSoftShadowMap;
renderer.outputColorSpace = THREE.SRGBColorSpace;
renderer.toneMapping = THREE.ACESFilmicToneMapping;
app.appendChild(renderer.domElement);

const scene = new THREE.Scene();
scene.background = new THREE.Color(0x7fb8dd); // clear day-blue sky
scene.fog = new THREE.Fog(0x7fb8dd, 60, 140);

const camera = new THREE.OrthographicCamera(-1,1,1,-1,0.1,300);
let camDist = 34, camAzim = Math.PI/4, camZoom = 22, viewZoom = 22; // frustum height
const _camTarget = new THREE.Vector3();
let _camInit = true;
function updateCameraFrustum(){
  const a = innerWidth/innerHeight;
  camera.left=-camZoom*a/2; camera.right=camZoom*a/2;
  camera.top=camZoom/2; camera.bottom=-camZoom/2;
  camera.updateProjectionMatrix();
  fpsCam.aspect=a; fpsCam.updateProjectionMatrix();
}
const fpsCam = new THREE.PerspectiveCamera(75, innerWidth/innerHeight, 0.1, 300);
scene.add(fpsCam); // needed so viewmodel gun renders
// simple FPS viewmodel rifle (only visible in 1st person)
const fpsGun = new THREE.Mesh(new THREE.BoxGeometry(0.07,0.1,0.7), new THREE.MeshLambertMaterial({color:0x2e2015}));
fpsGun.position.set(0.3,-0.27,-0.6); fpsCam.add(fpsGun);
const fpsGunTip = new THREE.Mesh(new THREE.BoxGeometry(0.05,0.05,0.12), new THREE.MeshBasicMaterial({color:0x555555}));
fpsGunTip.position.set(0.3,-0.24,-0.95); fpsCam.add(fpsGunTip);
fpsGun.visible=false; fpsGunTip.visible=false;
// view state: 'iso' = current diorama, 'fps' = 1st person
let viewMode='iso', fpsYaw=Math.PI, fpsPitch=0;
function toggleView(){
  viewMode = viewMode==='iso'?'fps':'iso';
  const b=document.getElementById('viewbtn');
  const fc=document.getElementById('fpscross');
  if(viewMode==='fps'){
    b.textContent='🎥 VIEW: 1ST PERSON [V] — click game to lock aim';
    fc.style.display='block';
    aimRing.visible=false; aimDot.visible=false;
    hunter.visible=false;
    fpsGun.visible=player.tool==='rifle'; fpsGunTip.visible=player.tool==='rifle';
    fpsRod.visible=player.tool==='rod'; fpsAxe.visible=player.tool==='axe';
    // start looking where the hunter was facing
    fpsYaw=wrapAng(hunter.rotation.y+Math.PI); // hunter faces +Z at yaw, camera looks -Z
    // hunter yaw = atan2(dx,dz); camera yaw for same dir = hunter yaw + PI (because cam looks -Z)
    fpsPitch=-0.05;
    renderer.domElement.requestPointerLock?.();
    toast('1ST PERSON — mouse to look, WASD move, click shoot');
  } else {
    b.textContent='🎥 VIEW: ISOMETRIC [V]';
    fc.style.display='none';
    aimRing.visible=true; aimDot.visible=true;
    hunter.visible=true;
    fpsGun.visible=false; fpsGunTip.visible=false; fpsRod.visible=false; fpsAxe.visible=false;
    document.exitPointerLock?.();
    toast('ISOMETRIC VIEW');
  }
}
updateCameraFrustum();
addEventListener('resize',()=>{renderer.setSize(innerWidth,innerHeight);updateCameraFrustum();});

// lights
const hemi=new THREE.HemisphereLight(0xfff2d8, 0x3a5a2a, 0.95);
scene.add(hemi);
const sun = new THREE.DirectionalLight(0xfff0c8, 1.9);
sun.position.set(28,42,14);
sun.castShadow = true;
sun.shadow.mapSize.set(2048,2048);
sun.shadow.camera.left=-30; sun.shadow.camera.right=30;
sun.shadow.camera.top=30; sun.shadow.camera.bottom=-30;
sun.shadow.camera.far=120;
scene.add(sun);
scene.add(new THREE.AmbientLight(0xffffff,0.15));

// ---------- helpers ----------
const MAT = c => new THREE.MeshLambertMaterial({color:c});
function box(w,h,d,color,x=0,y=0,z=0,parent=scene,ry=0){
  const m = new THREE.Mesh(new THREE.BoxGeometry(w,h,d), MAT(color));
  m.position.set(x,y,z); m.rotation.y=ry;
  m.castShadow=true; m.receiveShadow=true;
  parent.add(m); return m;
}
function rand(a,b){return a+Math.random()*(b-a)}
function randi(a,b){return Math.floor(rand(a,b+1))}
function pick(a){return a[Math.floor(Math.random()*a.length)]}
function noise2(x,z){ return Math.sin(x*0.7)*Math.cos(z*0.6)*0.5 + Math.sin(x*0.23+z*0.31)*0.8; }
// angle helpers — prevent 360° snaps when crossing -PI/PI or after many spins
function angDelta(target,current){ return Math.atan2(Math.sin(target-current),Math.cos(target-current)); }
function wrapAng(a){ return Math.atan2(Math.sin(a),Math.cos(a)); }

// ---------- audio (procedural, no files) ----------
let AC=null;
function audio(){ if(!AC) AC=new (window.AudioContext||window.webkitAudioContext)(); return AC; }
function shotSound(){
  try{
    const ac=audio(), t=ac.currentTime;
    const buf=ac.createBuffer(1,ac.sampleRate*0.25,ac.sampleRate);
    const d=buf.getChannelData(0);
    for(let i=0;i<d.length;i++) d[i]=(Math.random()*2-1)*Math.pow(1-i/d.length,2);
    const src=ac.createBufferSource(); src.buffer=buf;
    const f=ac.createBiquadFilter(); f.type='lowpass'; f.frequency.value=900;
    const g=ac.createGain(); g.gain.setValueAtTime(0.5,t); g.gain.exponentialRampToValueAtTime(0.01,t+0.25);
    src.connect(f); f.connect(g); g.connect(ac.destination); src.start();
    const o=ac.createOscillator(); o.type='square'; o.frequency.setValueAtTime(160,t); o.frequency.exponentialRampToValueAtTime(40,t+0.18);
    const g2=ac.createGain(); g2.gain.setValueAtTime(0.25,t); g2.gain.exponentialRampToValueAtTime(0.01,t+0.2);
    o.connect(g2); g2.connect(ac.destination); o.start(t); o.stop(t+0.2);
  }catch(e){}
}
function thudSound(){
  try{
    const ac=audio(),t=ac.currentTime;
    const o=ac.createOscillator(); o.type='sine'; o.frequency.setValueAtTime(220,t); o.frequency.exponentialRampToValueAtTime(60,t+0.15);
    const g=ac.createGain(); g.gain.setValueAtTime(0.4,t); g.gain.exponentialRampToValueAtTime(0.01,t+0.18);
    o.connect(g); g.connect(ac.destination); o.start(t); o.stop(t+0.2);
  }catch(e){}
}
function tone(f0,f1,dur,type='sine',vol=0.3,delay=0){
  try{
    const ac=audio(),t=ac.currentTime+delay;
    const o=ac.createOscillator(); o.type=type;
    o.frequency.setValueAtTime(f0,t); o.frequency.exponentialRampToValueAtTime(Math.max(f1,1),t+dur);
    const g=ac.createGain(); g.gain.setValueAtTime(vol,t); g.gain.exponentialRampToValueAtTime(0.01,t+dur);
    o.connect(g); g.connect(ac.destination); o.start(t); o.stop(t+dur+0.02);
  }catch(e){}
}
function levelupSound(){ tone(523,523,.12,'square',.18); tone(659,659,.12,'square',.18,.12); tone(784,1046,.25,'square',.2,.24); }
function coinSound(){ tone(988,1319,.12,'square',.15); tone(1319,1760,.18,'square',.12,.1); }
function missionSound(){ tone(392,392,.15,'triangle',.25); tone(523,523,.15,'triangle',.25,.14); tone(659,784,.3,'triangle',.25,.28); }
function howlSound(){
  try{
    const ac=audio(),t=ac.currentTime;
    const o=ac.createOscillator(); o.type='sine';
    o.frequency.setValueAtTime(300,t); o.frequency.linearRampToValueAtTime(620,t+0.5);
    o.frequency.linearRampToValueAtTime(450,t+1.1);
    const g=ac.createGain(); g.gain.setValueAtTime(0.0001,t);
    g.gain.exponentialRampToValueAtTime(0.22,t+0.2); g.gain.exponentialRampToValueAtTime(0.01,t+1.2);
    o.connect(g); g.connect(ac.destination); o.start(t); o.stop(t+1.25);
  }catch(e){}
}
let rainNodes=null;
function rainSound(on){
  try{
    const ac=audio();
    if(on&&!rainNodes){
      const len=ac.sampleRate*2, buf=ac.createBuffer(1,len,ac.sampleRate), d=buf.getChannelData(0);
      for(let i=0;i<len;i++) d[i]=Math.random()*2-1;
      const src=ac.createBufferSource(); src.buffer=buf; src.loop=true;
      const f=ac.createBiquadFilter(); f.type='highpass'; f.frequency.value=1200;
      const g=ac.createGain(); g.gain.setValueAtTime(0.0001,ac.currentTime);
      g.gain.exponentialRampToValueAtTime(0.06,ac.currentTime+2);
      src.connect(f); f.connect(g); g.connect(ac.destination); src.start();
      rainNodes={src,g};
    } else if(!on&&rainNodes){
      const {src,g}=rainNodes; rainNodes=null;
      g.gain.exponentialRampToValueAtTime(0.0001,ac.currentTime+1);
      setTimeout(()=>{ try{src.stop();}catch(e){} },1200);
    }
  }catch(e){}
}
function thunderSound(delay=0){
  try{
    const ac=audio(),t=ac.currentTime+delay;
    const o=ac.createOscillator(); o.type='sawtooth';
    o.frequency.setValueAtTime(70,t); o.frequency.exponentialRampToValueAtTime(28,t+1.4);
    const f=ac.createBiquadFilter(); f.type='lowpass'; f.frequency.value=160;
    const g=ac.createGain(); g.gain.setValueAtTime(0.0001,t);
    g.gain.exponentialRampToValueAtTime(0.3,t+0.08); g.gain.exponentialRampToValueAtTime(0.01,t+1.6);
    o.connect(f); f.connect(g); g.connect(ac.destination); o.start(t); o.stop(t+1.7);
  }catch(e){}
}
function splashSound(){
  try{
    const ac=audio(),t=ac.currentTime;
    const buf=ac.createBuffer(1,ac.sampleRate*0.3,ac.sampleRate), d=buf.getChannelData(0);
    for(let i=0;i<d.length;i++) d[i]=(Math.random()*2-1)*Math.pow(1-i/d.length,1.5);
    const src=ac.createBufferSource(); src.buffer=buf;
    const f=ac.createBiquadFilter(); f.type='bandpass'; f.frequency.value=1800;
    const g=ac.createGain(); g.gain.setValueAtTime(0.35,t); g.gain.exponentialRampToValueAtTime(0.01,t+0.3);
    src.connect(f); f.connect(g); g.connect(ac.destination); src.start();
  }catch(e){}
}
function plipSound(deep=false){
  tone(deep?300:700,deep?120:1200,.15,'sine',.25);
  if(!deep) setTimeout(()=>tone(900,1400,.12,'sine',.2),130);
}
function thunkSound(){ tone(130,55,.12,'square',.3); }
function crashSound(){
  try{
    const ac=audio(),t=ac.currentTime;
    const buf=ac.createBuffer(1,ac.sampleRate*0.4,ac.sampleRate), d=buf.getChannelData(0);
    for(let i=0;i<d.length;i++) d[i]=(Math.random()*2-1)*Math.pow(1-i/d.length,1.2);
    const src=ac.createBufferSource(); src.buffer=buf;
    const f=ac.createBiquadFilter(); f.type='lowpass'; f.frequency.value=500;
    const g=ac.createGain(); g.gain.setValueAtTime(0.4,t); g.gain.exponentialRampToValueAtTime(0.01,t+0.4);
    src.connect(f); f.connect(g); g.connect(ac.destination); src.start();
    tone(90,40,.3,'triangle',.3);
  }catch(e){}
}

// ---------- BIG WORLD + STEPPED VOXEL TERRAIN ----------
// Playable 96x96 diorama: hills, beaches, lakes, rim cliffs dropping to water.
const WORLD_R = 48;
const WATER_Y = -1.5;
const groundY = 0;
const solidTiles = new Set();
const key = (x,z)=>x+','+z;

function smoothH(x,z){
  let h = noise2(x*0.06,z*0.06)*2.2 + noise2(x*0.15+7,z*0.15-3)*0.7 + noise2(x*0.025-11,z*0.025+5)*3.5;
  // flat pads: cabin area + spawn meadow so buildings sit level
  const dCabin=Math.hypot(x+4,z+3), dSpawn=Math.hypot(x,z-8);
  const flat=Math.min(dCabin-5,dSpawn-5)/10; // <0 near pads
  const k=0.12+0.88*THREE.MathUtils.clamp(flat,0,1);
  h*=k;
  // rim falloff → cliffs + water at edges (floating-block feel from photo)
  const edge=Math.max(Math.abs(x),Math.abs(z));
  const fall=(edge-(WORLD_R-14))/14;
  if(fall>0) h-=fall*fall*11;
  return h;
}
function groundH(x,z){ return Math.round(smoothH(x,z)*2)/2; }
function isSolid(x,z){
  const xi=Math.round(x), zi=Math.round(z);
  if(Math.abs(xi)>WORLD_R||Math.abs(zi)>WORLD_R) return false;
  return solidTiles.has(key(xi,zi));
}
const _tileCol = new THREE.Color();
function tileColor(x,z,h){
  if(h<=-0.5) return _tileCol.setHex(0xd9c489); // sand
  if(h>=4.5) return _tileCol.setHex(0x8d8d94); // high rock
  if(h>=3.0) return _tileCol.setHex(pick([0x7a8a5a,0x8a8a72,0x6f7f52]));
  return _tileCol.setHex(pick([0x6da544,0x77b04b,0x63973c,0x7fba52,0x5d8f38]));
}

{
  const topGeo=new THREE.BoxGeometry(1,1,1);
  const topMat=new THREE.MeshLambertMaterial({color:0xffffff});
  const sideGeo=new THREE.BoxGeometry(1,1,1);
  const sideMat=new THREE.MeshLambertMaterial({color:0xffffff});
  const tops=[], sides=[];
  for(let x=-WORLD_R;x<=WORLD_R;x++)for(let z=-WORLD_R;z<=WORLD_R;z++){
    const edge=Math.max(Math.abs(x),Math.abs(z));
    const h=groundH(x,z);
    if(h<WATER_Y+0.3) continue; // lake / sea → water shows
    // organic bite on the rim only (keep interior fully playable)
    if(edge>WORLD_R-6 && (edge+noise2(x,z)*2.2>WORLD_R-0.8)) continue;
    if(edge>WORLD_R-4 && Math.random()<0.2) continue;
    solidTiles.add(key(x,z));
    tops.push({x,h,z});
    sides.push({x,h,z,edge});
  }
  const topInst=new THREE.InstancedMesh(topGeo,topMat,tops.length);
  const sideInst=new THREE.InstancedMesh(sideGeo,sideMat,sides.length);
  const d=new THREE.Object3D(); const c=new THREE.Color();
  tops.forEach((t,i)=>{
    d.position.set(t.x,t.h-0.5,t.z); d.scale.set(1,1,1); d.rotation.set(0,0,0); d.updateMatrix();
    topInst.setMatrixAt(i,d.matrix);
    tileColor(t.x,t.z,t.h); topInst.setColorAt(i,_tileCol);
  });
  sides.forEach((t,i)=>{
    const hh=t.h+6; // column from y=-7 up to h-1
    d.position.set(t.x,-7+hh/2,t.z); d.scale.set(0.98,hh,0.98); d.rotation.set(0,0,0); d.updateMatrix();
    sideInst.setMatrixAt(i,d.matrix);
    if(t.edge>WORLD_R-10) c.setHex(0x7a4430); // red rim cliffs like photo
    else c.setHex(0x6b4a33).offsetHSL(0,0,rand(-0.03,0.03));
    sideInst.setColorAt(i,c);
  });
  topInst.receiveShadow=true; topInst.castShadow=false;
  sideInst.receiveShadow=true;
  scene.add(topInst); scene.add(sideInst);
  // rock streaks on rim cliffs
  for(let i=0;i<40;i++){
    const a=Math.random()*Math.PI*2, r=WORLD_R-rand(1,6);
    const x=Math.round(Math.cos(a)*r), z=Math.round(Math.sin(a)*r);
    if(!solidTiles.has(key(x,z))) continue;
    const st=box(0.9,rand(0.8,1.8),0.9,0x4e2c20,x+rand(-0.2,0.2),groundH(x,z)-2.4,z+rand(-0.2,0.2));
    st.castShadow=false;
  }
  // solid base slab to close the floating block
  const base = box(WORLD_R*2-4,2,WORLD_R*2-4,0x4e2c20,0,-8,0);
  base.castShadow=false;
}
// water (lakes + sea around rim)
const waterGeo=new THREE.PlaneGeometry(600,600);
const waterMat=new THREE.MeshLambertMaterial({color:0x7fb8c9,transparent:true,opacity:0.9});
const water=new THREE.Mesh(waterGeo,waterMat);
water.rotation.x=-Math.PI/2; water.position.y=WATER_Y; water.receiveShadow=true;
scene.add(water);
// distant pink hills backdrop (pushed out for bigger world)
for(let i=0;i<10;i++){
  const a=i/10*Math.PI*2;
  const h=box(rand(24,40),rand(4,10),12,0x7a8fa0,Math.cos(a)*95,-3,Math.sin(a)*95,scene,-Math.PI/2+a);
  h.castShadow=false;
}
scene.fog=new THREE.Fog(0xdf9a98, 90, 260);
// sun covers bigger area now
sun.shadow.camera.left=-65; sun.shadow.camera.right=65;
sun.shadow.camera.top=65; sun.shadow.camera.bottom=-65;
sun.position.set(50,70,25);

// ---------- CABIN (ENTERABLE + DOLLHOUSE CUTAWAY) ----------
// I chose: roof lifts + fades when you're inside/close, and any wall
// between the camera and you fades to 15% — so you always see inside.
const cabin = new THREE.Group(); cabin.position.set(-4,groundH(-4,-3),-3); cabin.rotation.y=0.35; scene.add(cabin);
const WALL_H=2.4, WALL_T=0.25;
function wallBox(w,h,d,color,x,y,z){
  const m=box(w,h,d,color,x,y,z,cabin);
  m.material.transparent=true;
  return m;
}
box(5,0.15,4,0x7a5230,0,0.07,0,cabin); // floor
box(5.2,0.2,4.2,0x4a2815,0,-0.05,0,cabin); // foundation trim
const wallNorth=wallBox(5,WALL_H,WALL_T,0x6e3e22,0,1.2,-1.875);
const wallWest =wallBox(WALL_T,WALL_H,4,0x65401f,-2.375,1.2,0);
const wallEast =wallBox(WALL_T,WALL_H,4,0x65401f,2.375,1.2,0);
const wallSouthL=wallBox(2.45,WALL_H,WALL_T,0x6e3e22,-1.275,1.2,1.875);
const wallSouthR=wallBox(1.25,WALL_H,WALL_T,0x6e3e22,1.875,1.2,1.875);
const wallLintel=wallBox(1.3,0.6,WALL_T,0x6e3e22,0.6,2.1,1.875);
// open wooden door (swung open, doesn't block) + iron hinges + lantern
const doorPanel=box(0.9,1.8,0.08,0x2e1a0e,1.35,0.9,2.3,cabin,0.9);
box(0.12,0.12,0.04,0x222222,1.05,1.4,2.32,cabin); box(0.12,0.12,0.04,0x222222,1.05,0.5,2.32,cabin);
const lantern=new THREE.Mesh(new THREE.BoxGeometry(0.22,0.3,0.22),new THREE.MeshBasicMaterial({color:0xffd77a}));
lantern.position.set(-0.25,1.9,2.05); cabin.add(lantern);
box(0.26,0.06,0.26,0x222222,-0.25,2.08,2.05,cabin); // lantern cap
// windows with frames + shutters (glow) on walls that DON'T face the door
const winMat=new THREE.MeshBasicMaterial({color:0xffdf8a,transparent:true});
const winNorth=new THREE.Mesh(new THREE.BoxGeometry(0.9,0.7,0.1),winMat); winNorth.position.set(-1.2,1.5,-1.88); cabin.add(winNorth);
const winWest=new THREE.Mesh(new THREE.BoxGeometry(0.1,0.7,0.9),winMat.clone()); winWest.position.set(-2.38,1.5,0.2); cabin.add(winWest);
box(1.1,0.12,0.12,0x4a2e16,-1.2,1.92,-1.88,cabin); box(1.1,0.12,0.12,0x4a2e16,-1.2,1.08,-1.88,cabin); // lintel + sill
box(0.12,0.7,0.12,0x2e4a22,-1.85,1.5,-1.88,cabin); box(0.12,0.7,0.12,0x2e4a22,-0.55,1.5,-1.88,cabin); // shutters
// roof group — fades/lifts when inside
const cabinRoof=new THREE.Group(); cabin.add(cabinRoof);
const roofMats=[];
function roofSlab(w,h,d,color,x,y,z,rx){
  const m=new THREE.Mesh(new THREE.BoxGeometry(w,h,d),new THREE.MeshLambertMaterial({color,transparent:true}));
  m.position.set(x,y,z); m.rotation.x=rx; m.castShadow=true; cabinRoof.add(m); roofMats.push(m.material); return m;
}
roofSlab(5.6,0.22,2.7,0x8c2f26,0,3.55,-1.05,-0.55);
roofSlab(5.6,0.22,2.7,0x8c2f26,0,3.55,1.05,0.55);
roofSlab(5.3,0.25,0.4,0x6e231e,0,4.35,0,0); // ridge
// stepped gable fills (east/west) so the roof is enclosed from inside
for(const gx of [-2.375,2.375]){
  roofSlab(0.25,0.42,3.4,0x6e3e22,gx,2.62,0,0);
  roofSlab(0.25,0.42,2.6,0x6e3e22,gx,3.02,0,0);
  roofSlab(0.25,0.42,1.9,0x6e3e22,gx,3.42,0,0);
  roofSlab(0.25,0.42,1.2,0x6e3e22,gx,3.78,0,0);
  roofSlab(0.25,0.4,0.55,0x6e3e22,gx,4.08,0,0);
}
// eave filler boards close the wall-to-roof slit (north/south)
roofSlab(5.6,0.7,0.14,0x65401f,0,2.7,1.98,0);
roofSlab(5.6,0.7,0.14,0x65401f,0,2.7,-1.98,0);
const chimney=new THREE.Mesh(new THREE.BoxGeometry(0.5,1.6,0.5),new THREE.MeshLambertMaterial({color:0x6e6a66,transparent:true}));
chimney.position.set(-1.6,3.8,-0.5); chimney.castShadow=true; cabinRoof.add(chimney); roofMats.push(chimney.material);
const chimneyCap=new THREE.Mesh(new THREE.BoxGeometry(0.7,0.15,0.7),new THREE.MeshLambertMaterial({color:0x3a3a3a,transparent:true}));
chimneyCap.position.set(-1.6,4.65,-0.5); chimneyCap.castShadow=true; cabinRoof.add(chimneyCap); roofMats.push(chimneyCap.material);
let roofOp=1;
// ---- porch (south side, door at local x=0.6) ----
box(2.8,0.14,1.7,0x8a6238,0.6,0.07,2.85,cabin); // deck
box(2.8,0.1,0.3,0x6e4a2a,0.6,0.02,3.85,cabin); // step
for(const px of [-0.65,1.85]){
  box(0.18,2.0,0.18,0x5a3a1e,px,1.0,3.6,cabin); // porch posts
  box(0.3,0.12,0.3,0x8a8a86,px,0.06,3.6,cabin); // post footings
}
box(3.0,0.14,1.9,0x7a2822,0.6,2.05,2.85,cabin); // porch roof
box(2.5,0.1,0.12,0x5a3a1e,0.6,0.9,3.6,cabin); // railing between posts
const porchPostsWorld=[];
cabin.updateMatrixWorld(true);
for(const [lx,lz] of [[-0.65,3.6],[1.85,3.6]]){
  const v=new THREE.Vector3(lx,0,lz); cabin.localToWorld(v);
  porchPostsWorld.push({x:v.x,z:v.z,r:0.2});
}
// ---- interior (so going inside is worth it) ----
box(2.0,0.06,1.5,0x9a2f2f,0,0.12,0.2,cabin); // rug
box(1.4,0.1,0.8,0x8a5a30,-1.1,0.8,-0.6,cabin); // table top
[[-1.7,-0.9],[-0.5,-0.9],[-1.7,-0.3],[-0.5,-0.3]].forEach(([lx,lz])=>box(0.1,0.8,0.1,0x5a3a1e,lx,0.4,lz,cabin));
box(0.5,0.3,0.35,0xc94a4a,-1.1,1.0,-0.6,cabin,0.3); // meat on table 🍖
box(1.0,0.35,2.0,0x3a5a8a,1.45,0.3,-0.7,cabin); // bed
box(1.0,0.15,0.6,0xd8d0b8,1.45,0.55,-1.3,cabin); // pillow
box(0.9,1.1,0.4,0x4a2c14,0,1.7,-1.7,cabin); // shelf / gun rack
box(1.1,0.08,0.08,0x2e2015,0,1.75,-1.48,cabin); // rifle on rack
// ---- extra interior detail ----
for(const px of [-2,-1.2,-0.4,0.4,1.2,2]) box(0.68,0.02,3.9,0x6e4a2a,px,0.155,0,cabin); // floor planks
box(4.7,0.1,0.05,0x3a2412,0,0.2,-1.74,cabin); // baseboards
box(0.05,0.1,3.5,0x3a2412,-2.24,0.2,0,cabin); box(0.05,0.1,3.5,0x3a2412,2.24,0.2,0,cabin);
for(const bz of [-1,0,1]) box(5.0,0.14,0.18,0x3a2412,0,2.32,bz,cabin); // ceiling beams
// stone fireplace (east wall, south of bed) with ember glow
box(0.7,0.15,1.4,0x7a7a76,1.9,0.22,1.0,cabin); // hearth
box(0.5,0.9,0.25,0x7a7a76,1.95,0.6,0.4,cabin); box(0.5,0.9,0.25,0x7a7a76,1.95,0.6,1.6,cabin); // sides
box(0.5,0.25,1.45,0x7a7a76,1.95,1.15,1.0,cabin); // lintel
box(0.4,1.2,0.9,0x6e6e72,2.0,1.85,1.0,cabin); // chimney breast
const fpFire=new THREE.Mesh(new THREE.BoxGeometry(0.3,0.35,0.65),new THREE.MeshBasicMaterial({color:0xff8a3a}));
fpFire.position.set(1.85,0.5,1.0); cabin.add(fpFire);
box(0.12,0.12,0.6,0x2e1a0c,1.85,0.35,1.0,cabin); // fire logs
// stools
for(const [sx,sz] of [[-0.1,1.0],[-1.95,-0.1]]){
  box(0.15,0.4,0.15,0x5a3a1e,sx,0.35,sz,cabin); box(0.42,0.1,0.42,0x8a5a30,sx,0.6,sz,cabin);
}
// antler trophy (west wall) + barrel + sack + pantry shelf with jars
box(0.08,0.6,0.5,0x3a2a1a,-2.24,1.6,-0.9,cabin); // plaque
box(0.1,0.1,0.1,0xe8dcc8,-2.2,1.62,-0.9,cabin); // skull
box(0.06,0.4,0.06,0x4a3628,-2.2,1.85,-1.0,cabin,0.3); box(0.06,0.4,0.06,0x4a3628,-2.2,1.85,-0.8,cabin,0.3);
box(0.55,0.8,0.55,0x7a5a30,-1.9,0.55,1.2,cabin,0.2); // barrel
box(0.57,0.08,0.57,0x3a2a1a,-1.9,0.75,1.2,cabin,0.2); // barrel band
box(0.45,0.4,0.45,0xc9b98a,-1.25,0.35,1.35,cabin,0.3); // grain sack
box(0.3,0.08,1.0,0x8a5a30,2.08,1.5,-1.0,cabin); // pantry shelf
box(0.18,0.24,0.18,0xc93b2b,2.08,1.66,-1.25,cabin); // jars
box(0.18,0.24,0.18,0x5a8a3c,2.08,1.66,-1.0,cabin);
box(0.18,0.24,0.18,0xd9a441,2.08,1.66,-0.75,cabin);
box(1.02,0.1,1.1,0x9a2f2f,1.45,0.52,-0.4,cabin); // bed blanket
box(0.08,0.2,0.08,0xf2e6b8,-0.7,1.0,-0.4,cabin); // table candle
const candleFl=new THREE.Mesh(new THREE.BoxGeometry(0.05,0.09,0.05),new THREE.MeshBasicMaterial({color:0xffb84d}));
candleFl.position.set(-0.7,1.15,-0.4); cabin.add(candleFl);
const cabinLight=new THREE.PointLight(0xffc080,8,9,2); cabinLight.position.set(0,2.0,0); cabin.add(cabinLight);
// exterior: crates + firewood stack + chopping stump + tent (clear of door at local x=0.6, z=2)
box(1.2,0.6,0.6,0xd9a441,3.4,0.3,2.4,cabin,0.3);
box(0.8,0.8,0.8,0xb07a35,-3.4,0.4,2.6,cabin,0.5);
box(0.6,0.35,0.6,0x8a5a25,-2.6,0.18,2.9,cabin,0.2);
for(let r=0;r<3;r++) for(let i=0;i<3-r;i++) // log pyramid
  box(0.9,0.24,0.24,0x6e4a2a,-3.3+i*0.26+r*0.13,0.35+r*0.24,0.6,cabin,Math.PI/2);
box(0.5,0.5,0.5,0x7a5a38,-3.1,0.25,0.9,cabin,0.4); // stump (clear of west wall)
box(0.08,0.7,0.08,0x8a6238,-3.1,0.85,0.9,cabin); // axe handle, upright in stump
box(0.26,0.14,0.08,0x555555,-3.1,1.2,0.9,cabin); // axe head on top
box(1.4,1.0,1.4,0xd93b2b,3.6,0.5,-1.6,cabin,0.4); // tent
box(1.5,0.15,1.5,0x8a2f26,3.6,1.05,-1.6,cabin,0.4); // tent cap
// wall fade state: each wall + its outward normal (local)
const cabinWalls=[
  {m:wallNorth,n:[0,0,-1],op:1},
  {m:wallSouthL,n:[0,0,1],op:1},
  {m:wallSouthR,n:[0,0,1],op:1},
  {m:wallLintel,n:[0,0,1],op:1},
  {m:wallEast,n:[1,0,0],op:1},
  {m:wallWest,n:[-1,0,0],op:1},
];
let wasInside=false;

// water tower / windmill next to cabin
const tower=new THREE.Group(); tower.position.set(1.2,groundH(1.2,-4.5),-4.5); scene.add(tower);
[[-0.8,-0.8],[0.8,-0.8],[-0.8,0.8],[0.8,0.8]].forEach(([lx,lz])=>{
  const leg=box(0.25,5,0.25,0x7a5a38,lx,2.5,lz,tower); leg.rotation.z=lx*0.08; leg.rotation.x=-lz*0.08;
});
box(2.2,1.6,2.2,0x9a6a44,0,5.6,0,tower);
box(2.3,0.18,2.3,0x5a3a20,0,5.0,0,tower); // tank bands
box(2.3,0.18,2.3,0x5a3a20,0,6.2,0,tower);
box(2.4,0.3,2.4,0x6e3e22,0,6.5,0,tower);
box(0.5,1.0,0.5,0x6e3e22,0,7.1,0,tower);
box(2.0,0.12,2.0,0x6e3e22,0,4.15,0,tower); // platform
for(const [rx,rz] of [[0.35,0],[ -0.35,0],[0,0.35],[0,-0.35]]) // railing posts
  box(0.1,0.6,0.1,0x6e3e22,rx*4,4.5,rz*4,tower);
for(let i=0;i<5;i++){ // ladder rungs + rails (south face)
  box(0.5,0.08,0.08,0x4a2e16,0,1.2+i*0.7,1.05,tower);
}
box(0.08,3.6,0.08,0x4a2e16,-0.25,2.8,1.05,tower); box(0.08,3.6,0.08,0x4a2e16,0.25,2.8,1.05,tower);
// X cross-braces: each pair spans leg-to-leg inside its own face plane
{
  const y0=1.3, y1=3.9, ym=(y0+y1)/2;
  const bLen=Math.hypot(1.6,y1-y0)+0.15, bang=Math.atan2(1.6,y1-y0);
  for(const zz of [0.8,-0.8]) for(const s of [-1,1]){
    const b=box(0.1,bLen,0.1,0x5a4028,0,ym,zz,tower); b.rotation.z=s*bang; // front/back faces tilt in X
  }
  for(const xx of [0.8,-0.8]) for(const s of [-1,1]){
    const b=box(0.1,bLen,0.1,0x5a4028,xx,ym,0,tower); b.rotation.x=s*bang; // side faces tilt in Z
  }
}
const blades=new THREE.Group(); blades.position.set(0,6.2,1.25); tower.add(blades);
for(let i=0;i<4;i++){
  const b=box(0.28,1.3,0.08,0xd8cfb8,0,0.85,0,blades);
  box(0.44,0.5,0.06,0xc4b89e,0,0.8,0,b); // paddle tip
  const pivot=new THREE.Group(); pivot.rotation.z=i*Math.PI/2; pivot.add(b); blades.add(pivot);
  b.position.set(0,0.85,0);
}
box(0.5,0.3,0.12,0x8a2f26,0,6.2,0.5,tower); // tail vane
box(0.3,0.3,0.4,0x333333,0,6.2,1.15,tower);
// campfire: stone ring + teepee logs + layered flames + embers
const fire=new THREE.Group(); fire.position.set(-1.5,groundH(-1.5,2.5),2.5); scene.add(fire);
for(let i=0;i<8;i++){
  const a=i/8*Math.PI*2;
  box(0.28,0.22,0.28,0x7a7a76,Math.cos(a)*0.85,0.11,Math.sin(a)*0.85,fire,a);
}
for(let i=0;i<4;i++){
  const a=i/4*Math.PI*2+0.4;
  const log=box(0.14,1.0,0.14,0x4a2a12,Math.cos(a)*0.25,0.5,Math.sin(a)*0.25,fire,-a);
  log.rotation.z=Math.cos(a)*0.5; log.rotation.x=-Math.sin(a)*0.5;
}
box(0.5,0.12,0.5,0xff5a1a,0,0.12,0,fire,0.4); // glowing coal bed (emissive-ish)
const flame=new THREE.Mesh(new THREE.ConeGeometry(0.35,0.9,7),new THREE.MeshBasicMaterial({color:0xff8a2a}));
flame.position.y=0.8; fire.add(flame);
const flameIn=new THREE.Mesh(new THREE.ConeGeometry(0.18,0.55,7),new THREE.MeshBasicMaterial({color:0xffe28a}));
flameIn.position.y=0.7; fire.add(flameIn);
// spit: two posts + crossbar + hanging pot
box(0.1,1.1,0.1,0x5a3a1e,-0.9,0.55,0,fire); box(0.1,1.1,0.1,0x5a3a1e,0.9,0.55,0,fire);
box(1.9,0.08,0.08,0x5a3a1e,0,1.0,0,fire);
box(0.3,0.35,0.3,0x222226,0,0.68,0,fire); // pot
const fireLight=new THREE.PointLight(0xff8a3a,6,10,2); fireLight.position.set(-1.5,groundH(-1.5,2.5)+1.5,2.5); scene.add(fireLight);

// fences (follow terrain): posts + double rails + caps
for(let i=0;i<6;i++){ const fx=4+i*1.2, fy=groundH(fx,3.5);
  box(0.16,1.05,0.16,0x6e4a2a,fx,fy+0.5,3.5);
  box(0.24,0.08,0.24,0x5a3a20,fx,fy+1.05,3.5); // cap
}
for(const ry of [0.75,0.4]) box(6.2,0.1,0.1,0x7d5530,7,groundH(7,3.5)+ry,3.5); // rails

// ---------- BEACH FISHING SPOT (sand beside the house) ----------
const chairs=[];
function findBeach(){
  const cx=-4, cz=-3;
  for(let r=6;r<26;r+=2){
    for(let a=0;a<12;a++){
      const th=a/12*Math.PI*2;
      const x=Math.round(cx+Math.cos(th)*r), z=Math.round(cz+Math.sin(th)*r);
      if(Math.abs(x)>WORLD_R-3||Math.abs(z)>WORLD_R-3) continue;
      if(!isSolid(x,z)) continue;
      const h=groundH(x,z);
      if(h>-0.5||h<-1.2) continue; // sand band at the water's edge
      const ox=x-cx, oz=z-cz, ol=Math.hypot(ox,oz)||1;
      if(isSolid(Math.round(x+ox/ol*5),Math.round(z+oz/ol*5))) continue; // want open water that way
      return {x,z,dx:ox/ol,dz:oz/ol};
    }
  }
  return null;
}
function buildChair(x,z,ry,color){
  const gy=groundH(x,z);
  const g=new THREE.Group(); g.position.set(x,gy,z); g.rotation.y=ry; scene.add(g);
  const wood=0x7a5230;
  for(const [lx,lz] of [[-0.28,-0.22],[0.28,-0.22],[-0.28,0.22],[0.28,0.22]])
    box(0.1,0.45,0.1,wood,lx,0.22,lz,g); // legs
  box(0.7,0.12,0.6,color,0,0.48,0,g); // seat
  box(0.72,0.04,0.2,0xf2ede0,0,0.55,0.12,g); // towel stripe
  const back=box(0.7,0.7,0.12,color,0,0.9,-0.32,g); back.rotation.x=-0.15; // reclined back
  box(0.08,0.08,0.55,wood,-0.37,0.72,0,g); box(0.08,0.08,0.55,wood,0.37,0.72,0,g); // armrests
  return {x,z,ry,gy,dx:Math.sin(ry),dz:Math.cos(ry),mesh:g};
}
{
  const spot=findBeach();
  if(spot){
    const px=-spot.dz, pz=spot.dx; // perpendicular offset for the pair
    chairs.push(buildChair(spot.x+px*1.4,spot.z+pz*1.4,Math.atan2(spot.dx,spot.dz),0xd94f3d));
    chairs.push(buildChair(spot.x-px*1.4,spot.z-pz*1.4,Math.atan2(spot.dx,spot.dz),0x3d7bd9));
    const bx=spot.x-spot.dx*2.2, bz=spot.z-spot.dz*2.2, by=groundH(bx,bz);
    box(0.6,0.4,0.4,0x3d7bd9,bx,by+0.2,bz,scene,0.3); // cooler
    box(0.62,0.1,0.42,0xf2ede0,bx,by+0.42,bz,scene,0.3); // cooler lid
    const ux=spot.x-spot.dx*3.4, uz=spot.z-spot.dz*3.4, uy=groundH(ux,uz);
    box(0.1,2.2,0.1,0x8a6238,ux,uy+1.1,uz,scene); // umbrella pole
    const um=new THREE.Mesh(new THREE.ConeGeometry(1.1,0.6,8),new THREE.MeshLambertMaterial({color:0xf2e6b8}));
    um.position.set(ux,uy+2.3,uz); um.rotation.z=0.12; um.castShadow=true; scene.add(um);
  }
}
function nearChair(){
  for(const c of chairs){ if(Math.hypot(player.pos.x-c.x,player.pos.z-c.z)<1.8) return c; }
  return null;
}

// ---------- ROWBOAT at the fishing beach ----------
const boat={x:0,z:0,dir:0,mesh:null,oarL:null,oarR:null,ok:false};
{
  if(chairs.length){
    const c0=chairs[0];
    let bx=c0.x+c0.dx*4.5, bz=c0.z+c0.dz*4.5, tries=0;
    while(isSolid(Math.round(bx),Math.round(bz))&&tries<8){ bx+=c0.dx; bz+=c0.dz; tries++; }
    if(!isSolid(Math.round(bx),Math.round(bz))){
      boat.x=bx; boat.z=bz; boat.dir=Math.atan2(c0.dx,c0.dz); boat.ok=true;
      const g=new THREE.Group(); g.position.set(bx,WATER_Y+0.1,bz); g.rotation.y=boat.dir; scene.add(g);
      const wood=0x7a5230, dark=0x4a2e16;
      box(1.2,0.25,2.6,wood,0,0.15,0,g); // hull floor
      box(0.15,0.6,2.6,wood,-0.6,0.5,0,g); box(0.15,0.6,2.6,wood,0.6,0.5,0,g); // sides
      box(0.18,0.12,2.7,dark,-0.6,0.84,0,g); box(0.18,0.12,2.7,dark,0.6,0.84,0,g); // gunwales
      const bowL=box(0.5,0.5,0.7,wood,-0.35,0.45,1.5,g); bowL.rotation.y=0.5;
      const bowR=box(0.5,0.5,0.7,wood,0.35,0.45,1.5,g); bowR.rotation.y=-0.5;
      box(0.3,0.4,0.3,wood,0,0.45,1.7,g); // bow tip
      box(1.0,0.12,0.35,dark,0,0.55,-1.0,g); // stern bench
      box(1.0,0.12,0.35,dark,0,0.55,0.2,g); // mid bench
      const lant=new THREE.Mesh(new THREE.BoxGeometry(0.15,0.2,0.15),new THREE.MeshBasicMaterial({color:0xffd77a}));
      lant.position.set(0,1.05,-1.2); g.add(lant);
      const mkOar=sd=>{
        const og=new THREE.Group(); og.position.set(sd*0.65,0.7,0.2); g.add(og);
        box(1.6,0.07,0.07,0x8a6238,sd*0.8,0,0,og);
        box(0.3,0.05,0.25,0x8a6238,sd*1.6,-0.1,0,og);
        return og;
      };
      boat.oarL=mkOar(-1); boat.oarR=mkOar(1);
      boat.mesh=g;
      // little jetty back toward shore
      for(let i=1;i<=4;i++){
        const jx=bx-c0.dx*i*1.1, jz=bz-c0.dz*i*1.1;
        box(1.2,0.12,0.8,0x8a6238,jx,WATER_Y+0.35,jz,scene,Math.atan2(c0.dx,c0.dz));
        box(0.12,0.9,0.12,dark,jx+0.5,WATER_Y,jz,scene);
      }
    }
  }
}
function nearBoat(){
  if(!boat.ok||player.aboard) return null;
  return Math.hypot(player.pos.x-boat.x,player.pos.z-boat.z)<2.8?boat:null;
}
function boardBoat(){
  if(!boat.ok||player.sitting) return;
  player.pos.set(boat.x,0,boat.z); player.pos.y=WATER_Y+0.35;
  player.aboard=boat;
  tone(350,500,.15,'sine',.2);
  toast('🚣 Aboard! WASD to paddle • F to go ashore');
}
function findLanding(x,z){
  for(const r of [1.5,2,2.5,3.2]) for(let i=0;i<8;i++){
    const a=i/8*Math.PI*2, lx=x+Math.sin(a)*r, lz=z+Math.cos(a)*r;
    if(!isSolid(Math.round(lx),Math.round(lz))) continue;
    if(Math.abs(groundH(lx,lz)-WATER_Y)>2.5) continue; // must be low shore, not a cliff
    if(collide(lx,lz,0.4)) continue;
    return {x:lx,z:lz};
  }
  return null;
}
function disembark(){
  if(!player.aboard) return;
  const l=findLanding(player.pos.x,player.pos.z);
  if(!l){ toast('🚣 Too deep — paddle to shore'); tone(200,120,.15,'square',.15); return; }
  player.aboard=null;
  player.pos.set(l.x,0,l.z); player.pos.y=groundH(l.x,l.z);
  tone(500,350,.15,'sine',.2);
  toast('🦶 Ashore');
}
function waterOK(x,z){
  if(Math.abs(x)>WORLD_R+2||Math.abs(z)>WORLD_R+2) return false;
  return !isSolid(Math.round(x),Math.round(z));
}
let boatWakeT=0;
function moveBoat(mx,mz,dt){
  const bs=6*((keys['ShiftLeft']||keys['ShiftRight'])?1.35:1);
  const nx=player.pos.x+mx*bs*dt, nz=player.pos.z+mz*bs*dt;
  let moved=false;
  if(waterOK(nx,player.pos.z)){ player.pos.x=nx; moved=true; }
  if(waterOK(player.pos.x,nz)){ player.pos.z=nz; moved=true; }
  if(moved&&(mx||mz)){
    boat.dir+=angDelta(Math.atan2(mx,mz),boat.dir)*Math.min(1,dt*3);
    boatWakeT-=dt;
    if(boatWakeT<=0){
      boatWakeT=0.18;
      puff(new THREE.Vector3(player.pos.x-Math.sin(boat.dir)*1.4,player.pos.y-0.1,player.pos.z-Math.cos(boat.dir)*1.4),0xdfeaf2,2,0.12,9,2,0.6);
    }
  }
  player.pos.y=WATER_Y+0.35;
  boat.x=player.pos.x; boat.z=player.pos.z;
  boat.mesh.position.set(player.pos.x,WATER_Y+0.1+Math.sin(performance.now()*0.002)*0.08,player.pos.z);
  boat.mesh.rotation.y=boat.dir;
  return moved;
}

// (isSolid defined in terrain block above)
// cabin local coords (for doorway + cutaway + collision).
// NOTE: lives up here (not in the update section) so spawnWave() can use
// collide() during initial world build without hitting a TDZ error.
const _cabCos=Math.cos(0.35), _cabSin=Math.sin(0.35);
function cabinLocal(x,z){
  const dx=x-cabin.position.x, dz=z-cabin.position.z;
  return {x:dx*_cabCos-dz*_cabSin, z:dx*_cabSin+dz*_cabCos};
}
function cabinHit(x,z,r){
  const l=cabinLocal(x,z);
  // only near cabin
  if(Math.abs(l.x)>3.2||Math.abs(l.z)>2.8) return false;
  const t=WALL_T/2+r;
  if(Math.abs(l.z+1.875)<t && Math.abs(l.x)<2.5) return true; // north
  if(Math.abs(l.x+2.375)<t && Math.abs(l.z)<2.0) return true; // west
  if(Math.abs(l.x-2.375)<t && Math.abs(l.z)<2.0) return true; // east
  // south with doorway gap at x=0.6 width 1.3
  if(Math.abs(l.z-1.875)<t && Math.abs(l.x)<2.5){
    if(Math.abs(l.x-0.6)<0.65+r) return false; // through the door 🚪
    return true;
  }
  return false;
}
function collide(x,z,r=0.35){
  for(const t of treeColliders){ if(t.hidden) continue; if(Math.hypot(x-t.x,z-t.z)<r+t.r) return true; }
  if(cabinHit(x,z,r)) return true;
  for(const p of porchPostsWorld){ if(Math.hypot(x-p.x,z-p.z)<r+p.r) return true; }
  for(const c of chairs){ if(Math.hypot(x-c.x,z-c.z)<r+0.45) return true; }
  for(const s of structures){
    const sr=s.type==='wall'?1.3:s.type==='torch'?0.25:0;
    if(sr&&Math.hypot(x-s.x,z-s.z)<r+sr) return true;
  }
  if(Math.hypot(x-1.2,z+4.5)<1.1+r) return true; // tower
  return false;
}
// slope-aware step: solid + no tree/wall + can't scale cliffs (>1m step)
function stepOK(x0,z0,x1,z1,r=0.35){
  if(!isSolid(x1,z1)||collide(x1,z1,r)) return false;
  return Math.abs(groundH(x1,z1)-groundH(x0,z0))<=1.01;
}
// ---------- VEGETATION (pines like photo, seated on terrain) ----------
const treeColliders=[];
const fadeTrees=[]; // iso camera occlusion fade (3rd person only)
function regFade(g,x,z,r,baseY,topY,col){
  const mats=[], meshes=[];
  g.traverse(o=>{ if(o.isMesh){ mats.push(o.material); meshes.push(o); } });
  fadeTrees.push({g,mats,meshes,col,x,z,r,baseY,topY,op:1});
}
function slopeAt(x,z){
  const h0=groundH(x,z);
  return Math.max(Math.abs(groundH(x+1,z)-h0),Math.abs(groundH(x-1,z)-h0),Math.abs(groundH(x,z+1)-h0),Math.abs(groundH(x,z-1)-h0));
}
function pineTree(x,z,s=1){
  const gy=groundH(x,z);
  const g=new THREE.Group(); g.position.set(x,gy,z); g.rotation.y=Math.random()*Math.PI*2;
  box(0.55*s,0.3*s,0.55*s,0x4a2e16,0,0.15*s,0,g); // root flare
  box(0.34*s,1.7*s,0.34*s,0x5a3a1e,0,1.0*s,0,g); // trunk
  box(0.12*s,0.5*s,0.12*s,0x4a2e16,0.3*s,0.9*s,0.1*s,g,0.5); // branch stubs
  box(0.12*s,0.5*s,0.12*s,0x4a2e16,-0.28*s,1.2*s,-0.12*s,g,-0.4);
  const greens=[0x2e6b2e,0x35793a,0x2a5f2f];
  const snowy=gy>3.5;
  const layers=[[2.0,1.15,2.0],[1.6,1.0,2.8],[1.2,0.95,3.55],[0.8,0.85,4.2]];
  layers.forEach(([w,h,y],i)=>{
    const top=i>=2&&snowy;
    const m=box(w*s,h*s,w*s,top?0xdfe8ee:pick(greens),0,y*s,0,g,(i%2)*Math.PI/4);
    void m;
  });
  box(0.16*s,0.7*s,0.16*s,snowy?0xdfe8ee:0x2a5f2f,0,4.75*s,0,g); // spike
  scene.add(g); const colP={x,z,r:0.32*s,hidden:false}; treeColliders.push(colP); regFade(g,x,z,1.0*s,gy+1.4*s,gy+4.6*s,colP); return g;
}
function leafTree(x,z,s=1){
  const gy=groundH(x,z);
  const g=new THREE.Group(); g.position.set(x,gy,z); g.rotation.y=Math.random()*Math.PI*2;
  box(0.42*s,0.25*s,0.42*s,0x4a2e16,0,0.12*s,0,g); // roots
  const trunk=box(0.3*s,1.5*s,0.3*s,0x5a3a1e,0,0.85*s,0,g); trunk.rotation.z=0.06;
  box(1.5*s,1.1*s,1.4*s,0x4f9c3f,0.1*s,2.0*s,0,g,0.2); // canopy L
  box(1.2*s,1.0*s,1.2*s,0x66b84c,-0.35*s,2.5*s,0.15*s,g,-0.15); // canopy top
  box(0.9*s,0.7*s,0.9*s,0x7fca5f,0.25*s,2.9*s,-0.1*s,g,0.4); // highlight puff
  // apples
  if(Math.random()<0.7) for(let i=0;i<3;i++)
    box(0.14*s,0.14*s,0.14*s,0xd93b2b,rand(-0.6,0.7)*s,rand(1.7,2.4)*s,rand(0.5,0.75)*s,g);
  scene.add(g); const colL={x,z,r:0.30*s,hidden:false}; treeColliders.push(colL); regFade(g,x,z,1.0*s,gy+1.3*s,gy+3.1*s,colL);
}
function bush(x,z){
  const s=rand(0.5,0.9), gy=groundH(x,z), ry=Math.random()*3;
  box(s,s*0.7,s,pick([0x6fbf4a,0x86d452,0x5aa83c]),x,gy+s*0.3,z,scene,ry);
  box(s*0.6,s*0.45,s*0.6,0x8fdd5a,x,gy+s*0.62,z,scene,ry+0.5);
  if(Math.random()<0.5) for(let i=0;i<3;i++) // berries
    box(0.1,0.1,0.1,0xd93b5a,x+rand(-0.3,0.3)*s,gy+s*rand(0.4,0.65),z+rand(-0.3,0.3)*s,scene);
}
function rock(x,z){
  const s=rand(0.4,1.1), gy=groundH(x,z), ry=Math.random()*3;
  const m=box(s,s*0.7,s*0.8,0x8a8a86,x,gy+s*0.3,z,scene,ry);
  m.castShadow=true;
  box(s*0.5,s*0.35,s*0.45,0x7a7a76,x+s*0.5,gy+s*0.2,z+s*0.3,scene,ry+1); // satellite
  box(s*0.7,0.08,s*0.6,0x5da24a,x,gy+s*0.62,z,scene,ry); // moss cap
}
function mushroom(x,z){
  const gy=groundH(x,z);
  box(0.12,0.22,0.12,0xe8dcc8,x,gy+0.11,z,scene); // stem
  box(0.34,0.16,0.34,Math.random()<0.5?0xc93b2b:0xb06a2a,x,gy+0.28,z,scene,Math.random()*3); // cap
}
// scatter across the big world (density kept sane for perf)
const nearBase=(x,z)=>Math.hypot(x+4,z+3)<7||Math.hypot(x-1.2,z+4.5)<3||Math.hypot(x+1.5,z-2.5)<2.5;
let placed=0, guard=0;
while(placed<210 && guard++<4000){
  const x=rand(-WORLD_R+3,WORLD_R-3), z=rand(-WORLD_R+3,WORLD_R-3);
  if(!isSolid(x,z)) continue;
  const h=groundH(x,z);
  if(h<0||slopeAt(x,z)>1.0) continue; // no trees on beach / cliffs
  if(Math.hypot(x+4,z+3)<7) continue; // keep cabin clear
  if(Math.hypot(x,z-8)<5) continue; // keep spawn meadow clear
  let clear=true;
  for(const t of treeColliders){ if(Math.hypot(x-t.x,z-t.z)<2.6){ clear=false; break; } }
  if(!clear) continue; // no trunk clusters — animals must fit between
  if(Math.random()<0.62) pineTree(x,z,rand(0.8,1.6));
  else leafTree(x,z,rand(0.8,1.3));
  placed++;
}
for(let i=0;i<260;i++){
  const x=rand(-WORLD_R+2,WORLD_R-2),z=rand(-WORLD_R+2,WORLD_R-2);
  if(!isSolid(x,z)) continue;
  if(groundH(x,z)<-0.2||nearBase(x,z)) continue;
  const r=Math.random();
  if(r<0.5) bush(x,z); else if(r<0.85) rock(x,z); else mushroom(x,z);
}
// wildflowers (one instanced draw)
{
  const g=new THREE.BoxGeometry(0.13,0.22,0.13);
  const m=new THREE.MeshLambertMaterial({color:0xffffff});
  const inst=new THREE.InstancedMesh(g,m,300);
  const d=new THREE.Object3D(); const cols=[0xe84a5a,0xf2c14d,0xffffff,0xb678e8,0xef7d3c]; let n=0;
  const cc=new THREE.Color();
  for(let i=0;i<1200&&n<300;i++){
    const x=rand(-WORLD_R+2,WORLD_R-2),z=rand(-WORLD_R+2,WORLD_R-2);
    if(!isSolid(x,z)||nearBase(x,z)) continue;
    const h=groundH(x,z); if(h<0||h>3.5) continue;
    d.position.set(x,h+0.1,z); d.rotation.y=Math.random()*3; d.updateMatrix();
    inst.setMatrixAt(n,d.matrix); inst.setColorAt(n,cc.setHex(cols[n%cols.length]));
    n++;
  }
  inst.count=n; scene.add(inst);
}
// grass tufts (instanced cheap boxes)
{
  const g=new THREE.BoxGeometry(0.08,0.35,0.08);
  const m=new THREE.MeshLambertMaterial({color:0x8fdd5a});
  const inst=new THREE.InstancedMesh(g,m,1500);
  const d=new THREE.Object3D(); let n=0;
  for(let i=0;i<1500;i++){
    const x=rand(-WORLD_R+2,WORLD_R-2),z=rand(-WORLD_R+2,WORLD_R-2);
    if(!isSolid(x,z)||groundH(x,z)<0||nearBase(x,z)) continue;
    d.position.set(x,groundH(x,z)+0.15,z); d.rotation.y=Math.random()*3; d.updateMatrix();
    inst.setMatrixAt(n++,d.matrix);
  }
  inst.count=n; scene.add(inst);
}

// ---------- DAY/NIGHT CYCLE + TORCHES ----------
// 10-minute days. Torches/campfire/cabin glow at night, wolves get bolder in the dark.
let dayT=0.08, dayNum=1; const DAY_LEN=600; let sunElev=1;
const skyDay=new THREE.Color(0x7fb8dd), skyDusk=new THREE.Color(0xe2905a), skyNight=new THREE.Color(0x0b1026);
const sunNoon=new THREE.Color(0xfff0c8), sunLow=new THREE.Color(0xff7733);
const _sky=new THREE.Color();
const stars=(()=>{
  const n=350, pos=new Float32Array(n*3);
  for(let i=0;i<n;i++){
    const a=Math.random()*Math.PI*2, e=Math.random()*Math.PI*0.45+0.08, r=220;
    pos[i*3]=Math.cos(a)*Math.cos(e)*r; pos[i*3+1]=Math.sin(e)*r; pos[i*3+2]=Math.sin(a)*Math.cos(e)*r;
  }
  const g=new THREE.BufferGeometry(); g.setAttribute('position',new THREE.BufferAttribute(pos,3));
  const m=new THREE.PointsMaterial({color:0xcfd8ff,size:1.6,sizeAttenuation:false,transparent:true,opacity:0,fog:false,depthWrite:false});
  const p=new THREE.Points(g,m); scene.add(p); return p;
})();
const moon=new THREE.Mesh(new THREE.SphereGeometry(3,12,12),new THREE.MeshBasicMaterial({color:0xe8eeff,fog:false}));
scene.add(moon);
const moonLight=new THREE.DirectionalLight(0x8aa5ff,0);
moonLight.position.set(-40,50,-20); scene.add(moonLight);
const torches=[];
function addTorch(x,z){
  const g=new THREE.Group(); g.position.set(x,groundH(x,z),z); scene.add(g);
  box(0.16,1.3,0.16,0x4a2c14,0,0.65,0,g);
  box(0.2,0.12,0.2,0x222222,0,1.28,0,g); // iron band
  box(0.34,0.14,0.34,0x6e6e72,0,0.07,0,g); // stone footing
  const fl=new THREE.Mesh(new THREE.ConeGeometry(0.22,0.55,7),new THREE.MeshBasicMaterial({color:0xff9a2a}));
  fl.position.y=1.55; g.add(fl);
  const li=new THREE.PointLight(0xff9a3a,4,14,2); li.position.y=1.7; g.add(li);
  torches.push({fl,li});
}
addTorch(-6.5,-0.5); addTorch(-1.5,-5.5); addTorch(2.5,8);
function updateDaylight(dt){
  dayT+=dt/DAY_LEN;
  if(dayT>=1){ dayT-=1; dayNum++; toast(`☀️ Day ${dayNum} dawns`); }
  const a=dayT*Math.PI*2;
  sunElev=Math.sin(a);
  const dl=Math.min(1,Math.max(0,sunElev)); // 0 night → 1 full day
  // sun
  sun.intensity=1.9*dl;
  sun.position.set(Math.cos(a)*60,Math.max(5,sunElev*70),25);
  sun.color.copy(sunNoon).lerp(sunLow,1-Math.min(1,dl*2.5+0.2));
  moonLight.intensity=sunElev<0?0.3:0;
  moon.position.set(-Math.cos(a)*100,Math.max(10,-sunElev*100),-40);
  moon.visible=sunElev<0.05;
  hemi.intensity=0.25+0.7*dl;
  // sky + fog
  if(sunElev>0.25) _sky.copy(skyDay);
  else if(sunElev>-0.05) _sky.copy(skyDusk).lerp(skyDay,(sunElev+0.05)/0.3);
  else if(sunElev>-0.3) _sky.copy(skyNight).lerp(skyDusk,(sunElev+0.3)/0.25);
  else _sky.copy(skyNight);
  scene.background.copy(_sky); scene.fog.color.copy(_sky);
  stars.material.opacity=Math.min(1,Math.max(0,(-sunElev-0.05)*5))*0.9;
  // firelight grows at night (+flicker)
  const fl=1+Math.sin(performance.now()*0.013)*0.12+Math.sin(performance.now()*0.041)*0.06;
  fireLight.intensity=(3+7*(1-dl))*fl;
  cabinLight.intensity=(4+6*(1-dl))*fl;
  for(const t of torches){ t.li.intensity=(3+8*(1-dl))*fl; t.fl.scale.set(fl,1+ (fl-1)*2,fl); }
  // clock
  const hrs=Math.floor((6+dayT*24)%24), mins=Math.floor(((6+dayT*24)%1)*60);
  document.getElementById('clock').textContent=`${wxIcon()} Day ${dayNum} ${String(hrs).padStart(2,'0')}:${String(mins).padStart(2,'0')}`;
}

// ---------- WEATHER: clear / rain / fog ----------
// Cycles every minute or two. Rain hisses, fog hides you (and emboldens wolves).
let weather={type:'clear',timer:50};
let wxBlend=0, flashT=0, boltTimer=rand(6,14);
const wxRainTint=new THREE.Color(0x5a6a7a), wxFogTint=new THREE.Color(0x9aa5a8);
function isDark(){ return sunElev<=-0.05||weather.type==='fog'; }
function wxIcon(){ return weather.type==='rain'?'🌧️':weather.type==='fog'?'🌫️':(sunElev>-0.05?'☀️':'🌙'); }
const RAIN_N=260;
const rainGeo=new THREE.BufferGeometry();
const rainPos=new Float32Array(RAIN_N*6);
const rainVel=new Float32Array(RAIN_N);
function resetDrop(i,anyY){
  rainPos[i*6]=rand(-16,16); rainPos[i*6+2]=rand(-16,16);
  rainPos[i*6+1]=anyY?rand(-2,14):rand(8,14);
  rainPos[i*6+3]=rainPos[i*6]; rainPos[i*6+4]=rainPos[i*6+1]-0.7; rainPos[i*6+5]=rainPos[i*6+2];
  rainVel[i]=rand(18,26);
}
for(let i=0;i<RAIN_N;i++) resetDrop(i,true);
rainGeo.setAttribute('position',new THREE.BufferAttribute(rainPos,3));
const rainLines=new THREE.LineSegments(rainGeo,new THREE.LineBasicMaterial({color:0xaac4d8,transparent:true,opacity:0}));
rainLines.frustumCulled=false; rainLines.visible=false; scene.add(rainLines);
function setWeather(t){
  if(weather.type===t){ weather.timer=rand(50,110); return; }
  weather={type:t,timer:rand(60,130)};
  toast(t==='clear'?'☀️ Skies clearing':t==='rain'?'🌧️ Rain rolling in — prey scatters':'🌫️ Fog settling in — wolves grow bold');
}
function updateWeather(dt){
  weather.timer-=dt;
  if(weather.timer<=0){
    const opts=weather.type==='clear'?['rain','fog','clear','clear']:['clear','clear','rain','fog'];
    setWeather(pick(opts));
  }
  const target=weather.type==='clear'?0:1;
  wxBlend+=(target-wxBlend)*Math.min(1,dt*0.5);
  // rain streaks follow the player
  const raining=weather.type==='rain';
  rainLines.material.opacity+=((raining?0.45:0)-rainLines.material.opacity)*Math.min(1,dt*2);
  rainLines.visible=rainLines.material.opacity>0.02;
  if(rainLines.visible){
    const px=player.pos.x, pz=player.pos.z, gy=player.pos.y;
    for(let i=0;i<RAIN_N;i++){
      let x=rainPos[i*6], y=rainPos[i*6+1]-rainVel[i]*dt, z=rainPos[i*6+2];
      if(y<gy-2||Math.abs(x-px)>16||Math.abs(z-pz)>16){ x=px+rand(-16,16); z=pz+rand(-16,16); y=gy+rand(8,14); }
      rainPos[i*6]=x; rainPos[i*6+1]=y; rainPos[i*6+2]=z;
      rainPos[i*6+3]=x+0.15; rainPos[i*6+4]=y-0.7; rainPos[i*6+5]=z;
    }
    rainGeo.attributes.position.needsUpdate=true;
  }
  rainSound(raining&&started);
  // lightning
  if(raining){
    boltTimer-=dt;
    if(boltTimer<=0){ boltTimer=rand(7,18); flashT=0.18; thunderSound(rand(0.4,1.8)); }
  }
  if(flashT>0){ flashT-=dt; hemi.intensity+=flashT*10; }
  document.getElementById('flash').style.opacity=flashT>0?Math.max(0,flashT*3):0;
  // fog density drifts
  const fNear=weather.type==='fog'?12:weather.type==='rain'?45:90;
  const fFar=weather.type==='fog'?85:weather.type==='rain'?170:260;
  scene.fog.near+=(fNear-scene.fog.near)*Math.min(1,dt*0.5);
  scene.fog.far+=(fFar-scene.fog.far)*Math.min(1,dt*0.5);
  // gloom over the daylight sky
  if(wxBlend>0.01){
    _sky.lerp(weather.type==='fog'?wxFogTint:wxRainTint,wxBlend*0.45);
    scene.background.copy(_sky); scene.fog.color.copy(_sky);
    sun.intensity*=(1-wxBlend*0.75);
  }
}

// ---------- MINIMAP + COMPASS ----------
const MM_SIZE=150;
const mapBase=document.createElement('canvas'); mapBase.width=mapBase.height=MM_SIZE;
(function paintBase(){
  const c=mapBase.getContext('2d');
  c.fillStyle='#24425a'; c.fillRect(0,0,MM_SIZE,MM_SIZE); // sea
  const s=MM_SIZE/(WORLD_R*2+1);
  for(let x=-WORLD_R;x<=WORLD_R;x++)for(let z=-WORLD_R;z<=WORLD_R;z++){
    if(!solidTiles.has(key(x,z))) continue;
    const h=groundH(x,z);
    c.fillStyle=h<=-0.5?'#d9c489':h>=4.5?'#a8a8b0':h>=3?'#7d8a5f':h>=1.5?'#6da544':'#5d9440';
    c.fillRect((x+WORLD_R)*s,(z+WORLD_R)*s,Math.ceil(s),Math.ceil(s));
  }
})();
const mmC=document.getElementById('minimap').getContext('2d');
const w2m=v=>(v+WORLD_R)/(WORLD_R*2+1)*MM_SIZE;
let mmT=0;
function updateMinimap(dt){
  mmT-=dt; if(mmT>0) return; mmT=0.12; // 8Hz is plenty
  mmC.clearRect(0,0,MM_SIZE,MM_SIZE);
  mmC.drawImage(mapBase,0,0);
  mmC.fillStyle='#ff7a9a';
  for(const m of meats){ mmC.fillRect(w2m(m.position.x)-1,w2m(m.position.z)-1,2,2); }
  mmC.fillStyle='#ffb84d';
  mmC.fillRect(w2m(cabin.position.x)-3,w2m(cabin.position.z)-3,6,6);
  mmC.strokeStyle='#5a3a10'; mmC.strokeRect(w2m(cabin.position.x)-3,w2m(cabin.position.z)-3,6,6);
  mmC.fillStyle='#6e4a2a';
  for(const w of warrens){ mmC.beginPath(); mmC.arc(w2m(w.x),w2m(w.z),2,0,7); mmC.fill(); }
  mmC.fillStyle='#4dd2ff';
  for(const c of chairs){ mmC.fillRect(w2m(c.x)-2,w2m(c.z)-2,4,4); }
  if(boat.ok){ mmC.fillStyle='#ffffff'; mmC.fillRect(w2m(boat.x)-2,w2m(boat.z)-2,4,4); }
  mmC.fillStyle='#8a8a8a';
  for(const s of structures){ mmC.fillRect(w2m(s.x)-2,w2m(s.z)-2,4,4); }
  for(const a of animals){
    if(a.dead) continue;
    mmC.fillStyle=a.type==='wolf'?'#ff2a2a':a.type==='deer'?'#c98a4a':a.type==='boar'?'#6a4a30':'#f0f0f0';
    mmC.beginPath(); mmC.arc(w2m(a.pos.x),w2m(a.pos.z),a.type==='wolf'?3:2,0,7); mmC.fill();
  }
  // player arrow (north = -Z = up)
  const px=w2m(player.pos.x), py=w2m(player.pos.z);
  const fx=Math.sin(hunter.rotation.y), fz=Math.cos(hunter.rotation.y);
  mmC.save(); mmC.translate(px,py); mmC.rotate(Math.atan2(fx,-fz));
  mmC.fillStyle='#fff'; mmC.strokeStyle='#222'; mmC.lineWidth=1.5;
  mmC.beginPath(); mmC.moveTo(0,-7); mmC.lineTo(5,5); mmC.lineTo(-5,5); mmC.closePath(); mmC.fill(); mmC.stroke();
  mmC.restore();
  mmC.fillStyle='rgba(255,255,255,.9)'; mmC.font='bold 10px sans-serif'; mmC.fillText('N',4,12);
}
const cpC=document.getElementById('compass').getContext('2d');
function wrapDeg(d){ d%=360; return d<0?d+360:d; }
function compassHeading(){
  let lx,lz;
  if(viewMode==='fps'){ lx=-Math.sin(fpsYaw); lz=-Math.cos(fpsYaw); }
  else { lx=-Math.sin(camAzim); lz=-Math.cos(camAzim); }
  return wrapDeg(Math.atan2(lx,-lz)*180/Math.PI);
}
function updateCompass(){
  const W=260,H=30,hd=compassHeading(),ppd=2.6;
  cpC.clearRect(0,0,W,H);
  cpC.strokeStyle='rgba(255,255,255,.75)';
  cpC.font='bold 11px sans-serif'; cpC.textAlign='center';
  const names={0:'N',90:'E',180:'S',270:'W'};
  const start=Math.floor((hd-75)/15)*15;
  for(let deg=start;deg<=hd+75;deg+=15){
    const norm=((deg%360)+360)%360, x=W/2+(deg-hd)*ppd, isCard=norm%90===0;
    cpC.beginPath(); cpC.moveTo(x,isCard?8:13); cpC.lineTo(x,18); cpC.stroke();
    if(isCard){ cpC.fillStyle=norm===0?'#ff7a7a':'#fff'; cpC.fillText(names[norm],x,28); cpC.fillStyle='rgba(255,255,255,.75)'; }
  }
  const dot=(bearing,color)=>{
    let off=wrapDeg(bearing-hd); if(off>180)off-=360;
    if(Math.abs(off)>72) return;
    cpC.fillStyle=color; cpC.beginPath(); cpC.arc(W/2+off*ppd,5,3,0,7); cpC.fill();
  };
  for(const a of animals){
    if(a.dead||a.type!=='wolf') continue;
    const dx=a.pos.x-player.pos.x, dz=a.pos.z-player.pos.z;
    if(dx*dx+dz*dz>45*45) continue;
    dot(wrapDeg(Math.atan2(dx,-dz)*180/Math.PI),'#ff3b3b');
  }
  dot(wrapDeg(Math.atan2(cabin.position.x-player.pos.x,-(cabin.position.z-player.pos.z))*180/Math.PI),'#ffcf4d');
  cpC.fillStyle='#ffdf8a';
  cpC.beginPath(); cpC.moveTo(W/2-5,0); cpC.lineTo(W/2+5,0); cpC.lineTo(W/2,5); cpC.closePath(); cpC.fill();
}

// ---------- HUNTER ----------
const player={pos:new THREE.Vector3(0,groundH(0,8),8),vel:new THREE.Vector3(),yaw:Math.PI,speed:5,ammo:6,magSize:6,reloading:false,score:0,kills:0,meat:0,
  xp:0,level:1,coins:0,weapon:'rusty',owned:['rusty'],killsByType:{rabbit:0,boar:0,deer:0,wolf:0},meatCollected:0,soldOnce:false,wavesCleared:0,
  hp:100,maxHp:100,dead:false,invulnT:0,lastHurtT:-99,shakeT:0,
  tool:'rifle',sitting:null,aboard:null,fish:0,fishCaught:0,boatFish:0,wood:0,woodChopped:0,builtCount:0};
const WEAPONS={
  rusty:{name:'RUSTY RIFLE',dmg:1,mag:6,cd:0.28,range:60,price:0,desc:'Trusty starter'},
  hunter:{name:'HUNTER RIFLE',dmg:2,mag:8,cd:0.24,range:70,price:120,desc:'1-shot boar & deer'},
  sniper:{name:'LONGSHOT PRO',dmg:3,mag:5,cd:0.55,range:110,price:300,desc:'Huge range + RMB scope (FPS)'},
};
function weapon(){ return WEAPONS[player.weapon]; }
const hunter=new THREE.Group(); scene.add(hunter);
const hunterParts={};
{
  // legs on hip pivots (for walk swing) — boots attached below, swing together
  const limb=(px,py,pz,w,h,d,color)=>{
    const gr=new THREE.Group(); gr.position.set(px,py,pz); hunter.add(gr);
    box(w,h,d,color,0,-h/2,0,gr); return gr;
  };
  hunterParts.legL=limb(-0.18,0.78,0,0.3,0.62,0.34,0x4a4430);
  hunterParts.legR=limb(0.18,0.78,0,0.3,0.62,0.34,0x4a4430);
  box(0.34,0.18,0.44,0x2e2015,0,-0.69,0.04,hunterParts.legL); // boots ride on the feet
  box(0.34,0.18,0.44,0x2e2015,0,-0.69,0.04,hunterParts.legR);
  // torso: jacket + belt + collar
  box(0.78,0.7,0.48,0xb3542e,0,1.12,0,hunter);
  box(0.5,0.5,0.1,0x9a4526,0,1.12,0.26,hunter); // chest pocket flap
  box(0.8,0.14,0.5,0x3a2a1a,0,0.8,0,hunter); // belt
  box(0.2,0.14,0.06,0xd9a441,0,0.8,0.26,hunter); // belt buckle
  box(0.5,0.18,0.34,0x8a3f22,0,1.52,0,hunter); // collar
  // backpack + bedroll (seen from behind)
  box(0.52,0.62,0.26,0x5a4a2e,0,1.16,-0.37,hunter);
  box(0.54,0.14,0.28,0x3a5a8a,0,1.34,-0.37,hunter); // bedroll
  box(0.1,0.5,0.06,0x3a2e1c,-0.2,1.1,-0.5,hunter); // straps
  box(0.1,0.5,0.06,0x3a2e1c,0.2,1.1,-0.5,hunter);
  // arms on shoulder pivots, posed forward to HOLD the rifle
  hunterParts.armL=limb(-0.52,1.4,0,0.24,0.6,0.28,0xb3542e);
  hunterParts.armR=limb(0.52,1.4,0,0.24,0.6,0.28,0xb3542e);
  hunterParts.armL.userData.base=-0.85; hunterParts.armR.userData.base=-1.05;
  hunterParts.armL.rotation.x=-0.85; hunterParts.armR.rotation.x=-1.05;
  hunterParts.armL.rotation.z=0.35; // left hand crosses toward the gun
  box(0.22,0.16,0.24,0xe8b98a,0,-0.68,0,hunterParts.armL); // hands
  box(0.22,0.16,0.24,0xe8b98a,0,-0.68,0,hunterParts.armR);
  // head + face
  box(0.42,0.4,0.4,0xe8b98a,0,1.74,0,hunter);
  box(0.08,0.09,0.05,0x1a1a1a,-0.1,1.76,0.21,hunter); // eyes
  box(0.08,0.09,0.05,0x1a1a1a,0.1,1.76,0.21,hunter);
  box(0.3,0.07,0.05,0x5a3a22,0,1.63,0.21,hunter); // moustache
  box(0.42,0.08,0.36,0xc99878,0,1.56,0,hunter); // beard shadow
  // hat: brim + crown + band
  box(0.6,0.1,0.6,0x3d2c1a,0,1.97,0,hunter);
  box(0.34,0.26,0.34,0x4a3320,0,2.13,0,hunter);
  box(0.36,0.07,0.36,0xb3542e,0,2.04,0,hunter);
  box(0.1,0.1,0.06,0xd9a441,0.14,2.13,0.18,hunter); // hat pin
}
// rifle: stock + receiver + barrel + scope, held in both forward hands, pointing +Z
const rifle=new THREE.Group(); rifle.position.set(0.52,0.95,0.55); hunter.add(rifle); // gripped in right hand, clear of torso
box(0.1,0.18,0.4,0x5a3a1e,0,-0.02,0.0,rifle); // wooden stock (clear of chest)
box(0.09,0.12,0.55,0x2e2015,0,0.02,0.3,rifle); // receiver
box(0.05,0.05,0.5,0x141414,0,0.04,0.8,rifle); // barrel
box(0.06,0.06,0.1,0x141414,0,0.04,1.07,rifle); // muzzle
box(0.07,0.1,0.26,0x0e0e12,0,0.14,0.35,rifle); // scope
box(0.05,0.06,0.05,0x0e0e12,0,0.08,0.27,rifle); // scope mounts
box(0.05,0.06,0.05,0x0e0e12,0,0.08,0.43,rifle);
box(0.05,0.12,0.08,0x141414,0,-0.07,0.22,rifle); // trigger
box(0.07,0.16,0.1,0x5a3a1e,0,-0.08,0.48,rifle); // fore-grip in left hand
// fishing rod (swappable tool, held in right hand angled up-forward)
const rodG=new THREE.Group(); rodG.position.set(0.52,1.05,0.55); rodG.rotation.x=-0.15; rodG.visible=false; hunter.add(rodG);
box(0.09,0.55,0.09,0x6e4a2a,0,0.1,0,rodG); // cork handle
box(0.06,1.2,0.06,0x8a6a3a,0,0.85,0,rodG); // blank
box(0.035,0.5,0.035,0xd8cfb8,0,1.65,0,rodG); // pale tip
box(0.12,0.14,0.16,0xb03030,0,0.32,0.08,rodG); // reel
const rodTipLocal=new THREE.Vector3(0,1.9,0);
// FPS rod viewmodel
const fpsRod=new THREE.Group(); fpsRod.position.set(0.3,-0.27,-0.55); fpsRod.rotation.x=-0.4; fpsRod.visible=false; fpsCam.add(fpsRod);
{
  const rh=new THREE.Mesh(new THREE.BoxGeometry(0.06,0.4,0.06),new THREE.MeshLambertMaterial({color:0x6e4a2a})); fpsRod.add(rh);
  const rt=new THREE.Mesh(new THREE.BoxGeometry(0.03,0.9,0.03),new THREE.MeshLambertMaterial({color:0x8a6a3a})); rt.position.y=0.6; fpsRod.add(rt);
}
// firewood axe (third tool, key 3) + FPS viewmodel
const axeG=new THREE.Group(); axeG.position.set(0.52,0.95,0.55); axeG.rotation.x=0; axeG.visible=false; hunter.add(axeG);
box(0.07,0.7,0.07,0x8a6238,0,0,0,axeG);
box(0.24,0.14,0.06,0x8a8a90,0.08,0.32,0,axeG);
box(0.06,0.1,0.05,0x5a5a60,-0.08,0.32,0,axeG);
const fpsAxe=new THREE.Group(); fpsAxe.position.set(0.32,-0.32,-0.6); fpsAxe.rotation.x=-0.05; fpsAxe.visible=false; fpsCam.add(fpsAxe);
{
  const ah=new THREE.Mesh(new THREE.BoxGeometry(0.06,0.7,0.06),new THREE.MeshLambertMaterial({color:0x8a6238})); fpsAxe.add(ah);
  const ax=new THREE.Mesh(new THREE.BoxGeometry(0.05,0.13,0.28),new THREE.MeshLambertMaterial({color:0x8a8a90})); ax.position.set(0,0.36,-0.13); fpsAxe.add(ax);
  const ap=new THREE.Mesh(new THREE.BoxGeometry(0.06,0.1,0.08),new THREE.MeshLambertMaterial({color:0x5a5a60})); ap.position.set(0,0.36,0.06); fpsAxe.add(ap);
}
let swingT=0, stepT=0;
function setTool(t){
  if(player.tool===t) return;
  clearBobber();
  if(buildMode) exitBuild();
  player.tool=t;
  rifle.visible=t==='rifle'; rodG.visible=t==='rod'; axeG.visible=t==='axe';
  const inFps=viewMode==='fps';
  fpsGun.visible=t==='rifle'&&inFps; fpsGunTip.visible=t==='rifle'&&inFps;
  fpsRod.visible=t==='rod'&&inFps; fpsAxe.visible=t==='axe'&&inFps;
  toast(t==='rod'?'🎣 Rod equipped — aim at water, click to cast':t==='axe'?'⛏️ Axe equipped — click trees to chop':'🔫 Rifle equipped');
  refreshMetaHUD();
}
const aimRing=new THREE.Mesh(new THREE.RingGeometry(0.5,0.62,32),new THREE.MeshBasicMaterial({color:0xffffff,transparent:true,opacity:0.9,side:THREE.DoubleSide}));
aimRing.rotation.x=-Math.PI/2; aimRing.position.y=0.06; scene.add(aimRing);
const aimDot=new THREE.Mesh(new THREE.CircleGeometry(0.12,16),new THREE.MeshBasicMaterial({color:0xff4444}));
aimDot.rotation.x=-Math.PI/2; aimDot.position.y=0.07; scene.add(aimDot);

// ---------- BASE BUILDING: walls, spike traps, torches ----------
const structures=[];
const PIECES={
  wall:{name:'🧱 Wall',cost:8,desc:'Blocks animals'},
  spikes:{name:'🦔 Spikes',cost:5,desc:'2 dmg trap'},
  torch:{name:'🔥 Torch',cost:3,desc:'Night light'},
};
let buildMode=false, buildSel='wall', buildRy=0;
const buildGhost=new THREE.Group(); buildGhost.visible=false; scene.add(buildGhost);
let ghostMats=[];
function clearGhost(){
  buildGhost.traverse(o=>{ if(o.geometry) o.geometry.dispose(); if(o.material&&o.material.dispose) o.material.dispose(); });
  buildGhost.clear(); ghostMats=[];
}
function refreshGhost(){
  clearGhost();
  const gm=c=>{ const m=new THREE.MeshLambertMaterial({color:c,transparent:true,opacity:0.5,depthWrite:false}); m.userData.c=c; ghostMats.push(m); return m; };
  if(buildSel==='wall'){
    const w=new THREE.Mesh(new THREE.BoxGeometry(2.2,2,0.5),gm(0x8a6238)); w.position.y=1; buildGhost.add(w);
    for(const px of [-1,1]){ const p=new THREE.Mesh(new THREE.BoxGeometry(0.25,2.3,0.25),gm(0x5a3a1e)); p.position.set(px,1.15,0); buildGhost.add(p); }
  } else if(buildSel==='spikes'){
    const b=new THREE.Mesh(new THREE.BoxGeometry(1.4,0.2,1.4),gm(0x6e4a2a)); b.position.y=0.1; buildGhost.add(b);
    const sm=gm(0xd8d0c0);
    for(const [sx,sz] of [[0,0],[0.45,0.45],[-0.45,0.45],[0.45,-0.45],[-0.45,-0.45]]){
      const s=new THREE.Mesh(new THREE.ConeGeometry(0.12,0.9,5),sm); s.position.set(sx,0.6,sz); buildGhost.add(s);
    }
  } else {
    const p=new THREE.Mesh(new THREE.BoxGeometry(0.16,1.3,0.16),gm(0x4a2c14)); p.position.y=0.65; buildGhost.add(p);
    const f=new THREE.Mesh(new THREE.ConeGeometry(0.22,0.55,7),new THREE.MeshBasicMaterial({color:0xff9a2a,transparent:true,opacity:0.6})); f.position.y=1.55; f.material.userData={}; buildGhost.add(f); ghostMats.push(f.material); f.material.userData.c=0xff9a2a;
  }
}
function ghostSpot(){
  // returns {x,z} under the ghost cursor
  if(viewMode==='fps'){
    const eye=new THREE.Vector3(player.pos.x,player.pos.y+1.6,player.pos.z);
    const dir=new THREE.Vector3(0,0,-1).applyEuler(new THREE.Euler(fpsPitch,fpsYaw,0,'YXZ'));
    for(let d=2;d<14;d+=0.5){
      const x=eye.x+dir.x*d, z=eye.z+dir.z*d;
      if(isSolid(Math.round(x),Math.round(z))) return {x,z};
    }
    const x=eye.x+dir.x*6, z=eye.z+dir.z*6;
    return {x,z};
  }
  return {x:aimPoint.x,z:aimPoint.z};
}
function ghostCheck(x,z){
  if(player.wood<PIECES[buildSel].cost) return 'Need wood — chop trees (3)';
  if(!isSolid(Math.round(x),Math.round(z))) return 'No ground here';
  if(slopeAt(x,z)>1) return 'Too steep';
  if(Math.hypot(x-cabin.position.x,z-cabin.position.z)>26) return 'Too far — build near the cabin';
  if(collide(x,z,0.9)) return 'Blocked';
  return null;
}
function updateGhost(){
  buildGhost.visible=buildMode;
  if(!buildMode) return;
  const s=ghostSpot();
  buildGhost.position.set(s.x,groundH(s.x,s.z),s.z);
  buildGhost.rotation.y=buildRy;
  const bad=ghostCheck(s.x,s.z);
  for(const m of ghostMats) m.color.setHex(bad?0xff3333:(m.userData.c||0xffffff));
  buildGhost.userData.bad=bad;
}
function buildStructure(p){
  const g=new THREE.Group(); g.position.set(p.x,groundH(p.x,p.z),p.z); g.rotation.y=p.ry||0; scene.add(g);
  p.mesh=g;
  if(p.type==='wall'){
    box(2.2,2,0.5,0x8a6238,0,1,0,g);
    box(0.25,2.3,0.25,0x5a3a1e,-1,1.15,0,g); box(0.25,2.3,0.25,0x5a3a1e,1,1.15,0,g);
    box(2.3,0.15,0.6,0x6e4a2a,0,2.05,0,g); // cap rail
    p.r=1.3;
  } else if(p.type==='spikes'){
    box(1.4,0.2,1.4,0x6e4a2a,0,0.1,0,g);
    for(const [sx,sz] of [[0,0],[0.45,0.45],[-0.45,0.45],[0.45,-0.45],[-0.45,-0.45]]){
      const s=new THREE.Mesh(new THREE.ConeGeometry(0.12,0.9,5),new THREE.MeshLambertMaterial({color:0xd8d0c0}));
      s.position.set(sx,0.6,sz); s.castShadow=true; g.add(s);
    }
    p.r=0;
  } else {
    box(0.16,1.3,0.16,0x4a2c14,0,0.65,0,g);
    const fl=new THREE.Mesh(new THREE.ConeGeometry(0.22,0.55,7),new THREE.MeshBasicMaterial({color:0xff9a2a}));
    fl.position.y=1.55; g.add(fl);
    const li=new THREE.PointLight(0xff9a3a,4,14,2); li.position.y=1.7; g.add(li);
    p.tref={fl,li}; torches.push(p.tref);
    p.r=0.2;
  }
  structures.push(p);
}
function placePiece(){
  if(!buildMode||player.dead||shopOpen) return false;
  const now=performance.now();
  const deny=msg=>{ if(now-lastBuildToast>1200){ lastBuildToast=now; toast('🚧 '+msg); tone(200,120,.15,'square',.15); } };
  if(player.wood<PIECES[buildSel].cost){ deny('Need wood — chop trees (3)'); return false; }
  const bad=buildGhost.userData.bad||ghostCheck(buildGhost.position.x,buildGhost.position.z);
  if(bad){ deny(bad); return false; }
  player.wood-=PIECES[buildSel].cost; player.builtCount++;
  buildStructure({type:buildSel,x:buildGhost.position.x,z:buildGhost.position.z,ry:buildRy});
  thunkSound();
  puff(new THREE.Vector3(buildGhost.position.x,groundH(buildGhost.position.x,buildGhost.position.z)+1,buildGhost.position.z),0xc9a86a,8,0.12,9,4,0.6);
  refreshMetaHUD(); updateMissions(); saveGame(); renderBuildBar();
  return true;
}
let lastBuildToast=0;
function renderBuildBar(){
  const bar=document.getElementById('buildbar');
  bar.style.display=buildMode?'flex':'none';
  if(!buildMode) return;
  bar.innerHTML='';
  for(const id of Object.keys(PIECES)){
    const pc=PIECES[id], afford=player.wood>=pc.cost;
    const d=document.createElement('div');
    d.className='bopt'+(id===buildSel?' sel':'')+(afford?'':' cant');
    d.innerHTML=`<b>${id==='wall'?'1':id==='spikes'?'2':'3'} ${pc.name}</b><small>${pc.cost}🪵 • ${pc.desc}</small>`;
    d.onclick=e=>{ e.stopPropagation(); selectPiece(id); };
    bar.appendChild(d);
  }
}
function selectPiece(id){
  if(!PIECES[id]) return;
  buildSel=id; refreshGhost(); renderBuildBar();
  tone(500,700,.08,'square',.12);
}
function enterBuild(){
  if(player.sitting){ toast('Stand up to build (E)'); return; }
  buildMode=true; buildRy=0; refreshGhost(); renderBuildBar(); updateGhost();
  document.exitPointerLock?.();
  toast('🔨 Build mode — 1/2/3 piece • Click place • R rotate • B exit');
}
function exitBuild(){
  buildMode=false; buildGhost.visible=false; renderBuildBar();
}

// ---------- ANIMALS (voxel, wander/graze/flee) ----------
const animals=[];
const TYPES={
  rabbit:{hp:1,score:10,speed:3.2,scale:0.55,body:0xcfc4b0,ear:0xd8cdb8,name:'RABBIT'},
  boar:{hp:2,score:25,speed:2.6,scale:0.9,body:0x4a3628,name:'BOAR'},
  deer:{hp:2,score:40,speed:3.0,scale:1.15,body:0x9a6a3a,name:'DEER'},
  wolf:{hp:3,score:50,speed:4.3,scale:1.0,body:0x5c5c66,name:'WOLF'},
};
function makeAnimal(type,x,z){
  const cfg=TYPES[type], s=cfg.scale;
  const g=new THREE.Group(); g.position.set(x,groundH(x,z),z);
  const parts={legs:[]};
  const B=(w,h,d,c,px,py,pz,rx=0,ry=0,rz=0)=>{ const m=box(w*s,h*s,d*s,c,px*s,py*s,pz*s,g); m.rotation.set(rx,ry,rz); return m; };
  // legs on shoulder/hip pivots: upper + dark hoof
  const leg=(lx,lz,upper=0.16,len=0.55)=>{
    const gr=new THREE.Group(); gr.position.set(lx*s,0.58*s,lz*s); g.add(gr);
    box(upper*s,(len-0.1)*s,upper*s,0x3a2a1e,0,-(len-0.1)*s/2,0,gr);
    box((upper+0.02)*s,0.12*s,(upper+0.02)*s,0x1e150e,0,-len*s+0.06*s,0,gr); // hoof
    parts.legs.push(gr); return gr;
  };
  const eyes=(y,z2,spread,col=0x111111)=>{
    box(0.12*s,0.12*s,0.06*s,col,spread*s,y*s,z2*s,g); box(0.12*s,0.12*s,0.06*s,col,-spread*s,y*s,z2*s,g);
  };
  if(type==='deer'){
    B(0.62,0.62,1.25,cfg.body,0,0.95,0); // torso
    B(0.66,0.66,0.5,0xa8763f,0,0.98,0.45); // chest
    B(0.5,0.3,0.9,0xe8d8b8,0,0.68,0.05); // cream belly
    B(0.2,0.2,0.3,0x8a5f30,0,0.95,-0.72); // tail nub
    B(0.1,0.1,0.1,0x2e1c10,0,0.98,-0.88); // tail tip
    B(0.34,0.5,0.3,cfg.body,0,1.35,0.72,0.25); // neck, leaning forward
    parts.head=B(0.44,0.42,0.5,cfg.body,0,1.62,0.92); // head
    B(0.26,0.24,0.3,0x8a5f30,0,1.54,1.2); // muzzle
    B(0.12,0.1,0.08,0x1a100a,0,1.58,1.36); // nose
    eyes(1.7,1.16,0.14);
    B(0.14,0.3,0.1,0x8a5f30,-0.2,1.85,0.8,0,0,0.25); // ears
    B(0.14,0.3,0.1,0x8a5f30,0.2,1.85,0.8,0,0,-0.25);
    // branched antlers
    for(const sd of [-1,1]){
      B(0.07,0.55,0.07,0x4a3628,sd*0.2,1.95,0.78,0,0,sd*-0.3);
      B(0.28,0.06,0.06,0x4a3628,sd*0.32,1.98,0.78);
      B(0.2,0.06,0.06,0x4a3628,sd*0.3,2.12,0.78);
      B(0.06,0.18,0.06,0x4a3628,sd*0.4,2.2,0.78);
    }
    // white flank spots
    B(0.05,0.12,0.12,0xf0e6d0,0.32,1.05,-0.1); B(0.05,0.12,0.12,0xf0e6d0,-0.32,1.05,-0.1);
    B(0.05,0.12,0.12,0xf0e6d0,0.32,1.05,0.2); B(0.05,0.12,0.12,0xf0e6d0,-0.32,1.05,0.2);
    leg(-0.22,0.42); leg(0.22,0.42); leg(-0.22,-0.42); leg(0.22,-0.42);
  }
  if(type==='boar'){
    B(0.8,0.7,1.2,cfg.body,0,0.8,0); // barrel torso
    B(0.5,0.3,1.0,0x2e2118,0,1.2,-0.05); // dark mane ridge
    B(0.3,0.2,0.2,0x2e2118,0,1.28,0.5); // mane over shoulders
    B(0.15,0.15,0.4,0x2e2118,0,0.75,-0.7); // thin tail
    parts.head=B(0.56,0.56,0.5,0x52402e,0,1.0,0.78); // big head
    B(0.4,0.28,0.28,0x2e2118,0,0.88,1.1); // snout disc
    B(0.08,0.08,0.06,0x0e0a06,-0.1,0.88,1.25); B(0.08,0.08,0.06,0x0e0a06,0.1,0.88,1.25); // nostrils
    B(0.1,0.28,0.1,0xe8dcc0,-0.26,0.78,1.12,0,0,0.5); // tusks curving up
    B(0.1,0.28,0.1,0xe8dcc0,0.26,0.78,1.12,0,0,-0.5);
    eyes(1.12,1.02,0.18);
    B(0.16,0.22,0.1,0x3a2c1e,-0.24,1.3,0.7); B(0.16,0.22,0.1,0x3a2c1e,0.24,1.3,0.7); // small ears
    leg(-0.26,0.4,0.18,0.5); leg(0.26,0.4,0.18,0.5); leg(-0.26,-0.4,0.18,0.5); leg(0.26,-0.4,0.18,0.5);
  }
  if(type==='rabbit'){
    B(0.5,0.5,0.8,cfg.body,0,0.55,0); // torso
    B(0.24,0.4,0.4,cfg.body,-0.28,0.5,-0.25); B(0.24,0.4,0.4,cfg.body,0.28,0.5,-0.25); // haunches
    B(0.2,0.16,0.3,0xbfb49c,0,0.32,0.35); // chest fluff
    parts.head=B(0.38,0.36,0.4,cfg.body,0,0.95,0.55);
    B(0.14,0.12,0.1,0xe89a9a,0,0.88,0.76); // pink nose
    eyes(1.02,0.74,0.12);
    B(0.13,0.55,0.1,cfg.ear,-0.12,1.4,0.45,0,0,0.12); // long ears
    B(0.13,0.55,0.1,cfg.ear,0.12,1.4,0.45,0,0,-0.12);
    B(0.07,0.35,0.05,0xe89a9a,-0.12,1.38,0.5,0,0,0.12); // pink inner ear
    B(0.07,0.35,0.05,0xe89a9a,0.12,1.38,0.5,0,0,-0.12);
    B(0.24,0.24,0.3,0xffffff,0,0.6,-0.55); // powder tail
    leg(-0.16,0.28,0.12,0.42); leg(0.16,0.28,0.12,0.42);
    leg(-0.18,-0.3,0.15,0.45); leg(0.18,-0.3,0.15,0.45);
  }
  if(type==='wolf'){
    B(0.6,0.6,1.3,cfg.body,0,0.88,0); // torso
    B(0.55,0.5,0.4,0x3a3a44,0,1.05,0.5); // neck ruff
    B(0.45,0.25,1.0,0xd8d4c8,0,0.62,0.1); // pale underbelly
    parts.head=B(0.44,0.42,0.48,0x55555e,0,1.28,0.82);
    B(0.36,0.22,0.3,0x2c2c33,0,1.16,1.08); // dark snout
    B(0.1,0.08,0.06,0x0c0c0e,0,1.2,1.24); // nose
    box(0.1*s,0.1*s,0.05*s,0xff2a2a,0.13*s,1.34*s,1.07*s,g); // red eyes
    box(0.1*s,0.1*s,0.05*s,0xff2a2a,-0.13*s,1.34*s,1.07*s,g);
    B(0.14,0.4,0.12,0x3a3a42,-0.14,1.62,0.72,0,0,0.15); // erect ears
    B(0.14,0.4,0.12,0x3a3a42,0.14,1.62,0.72,0,0,-0.15);
    B(0.18,0.18,0.45,0x33333a,0,1.0,-0.85,-0.5); // tail base up
    B(0.15,0.15,0.4,0x3d3d46,0,1.2,-1.1,-0.9); // bushy tail tip
    leg(-0.2,0.45); leg(0.2,0.45); leg(-0.2,-0.45); leg(0.2,-0.45);
  }
  scene.add(g);
  parts.headY=parts.head.position.y;
  const a={type,cfg,mesh:g,parts,pos:g.position,hp:cfg.hp,maxHp:cfg.hp,state:'graze',t:rand(1,3),dir:rand(0,Math.PI*2),speed:0,dead:false,deadT:0,phase:rand(0,9),biteCD:0,herd:null,bleed:0,bloodT:0,printT:Math.random()};
  animals.push(a); return a;
}
const warrens=[];
function warrenSpot(){
  for(let t=0;t<60;t++){
    const x=rand(-WORLD_R+8,WORLD_R-8), z=rand(-WORLD_R+8,WORLD_R-8);
    if(!isSolid(x,z)||groundH(x,z)<0||slopeAt(x,z)>0.5) continue;
    if(Math.hypot(x+4,z+3)<10||Math.hypot(x,z-8)<8) continue;
    if(warrens.some(w=>Math.hypot(x-w.x,z-w.z)<18)) continue;
    return {x,z};
  }
  return null;
}
function buildWarren(w){
  const gy=groundH(w.x,w.z);
  box(1.4,0.4,1.4,0x8a6a42,w.x,gy+0.15,w.z,scene,0.4); // mound
  const hole=new THREE.Mesh(new THREE.CircleGeometry(0.45,12),new THREE.MeshBasicMaterial({color:0x0c0805}));
  hole.rotation.x=-Math.PI/2; hole.position.set(w.x,gy+0.37,w.z); scene.add(hole);
}
function findSpot(minPlayerDist){
  for(let tries=0;tries<80;tries++){
    const x=rand(-WORLD_R+5,WORLD_R-5), z=rand(-WORLD_R+5,WORLD_R-5);
    if(!isSolid(x,z)||groundH(x,z)<0||slopeAt(x,z)>1.0||collide(x,z,0.5)) continue;
    if(Math.hypot(x-player.pos.x,z-player.pos.z)<minPlayerDist) continue;
    return {x,z};
  }
  return null;
}
function spawnHerd(type,count,at){
  let leader=null;
  for(let i=0;i<count;i++){
    const x=at.x+rand(-3,3), z=at.z+rand(-3,3);
    if(!isSolid(x,z)||groundH(x,z)<0) continue;
    const a=makeAnimal(type,x,z);
    if(!leader){ leader=a; } else a.herd=leader;
  }
}
function spawnWave(n){
  if(!warrens.length){ for(let i=0;i<3;i++){ const w=warrenSpot(); if(w){ warrens.push(w); buildWarren(w); } } }
  const mix=['deer','deer','boar','boar','rabbit','rabbit','rabbit'];
  let left=n;
  if(left>=4){ const at=findSpot(12); if(at){ const c=Math.min(3+Math.floor(Math.random()*3),left); spawnHerd('deer',c,at); left-=c; } }
  if(left>=3){ const at=findSpot(12); if(at){ spawnHerd('boar',2,at); left-=2; } }
  if(left>=2&&warrens.length){ const w=pick(warrens); const c=Math.min(3,left); spawnHerd('rabbit',c,{x:w.x+rand(-2,2),z:w.z+rand(-2,2)}); left-=c; }
  while(left-->0){
    const s=findSpot(10); if(!s) break;
    makeAnimal(pick(mix),s.x,s.z);
  }
  // wolves hunt from wave 2 — small packs at the edges, far from player
  const wolves=wave<2?0:wave<3?1:2;
  for(let i=0;i<wolves;i++){
    let x=0,z=0,tries=0;
    do{
      const edge=randi(0,3);
      const r=rand(WORLD_R-14,WORLD_R-6);
      x=edge===0?-r:edge===1?r:rand(-r,r);
      z=edge<2?rand(-r,r):(edge===2?-r:r);
      tries++;
    }
    while((!isSolid(x,z)||groundH(x,z)<0||slopeAt(x,z)>1.0||collide(x,z,0.5)||Math.hypot(x-player.pos.x,z-player.pos.z)<18)&&tries<80);
    if(tries>=80) continue;
    const w=makeAnimal('wolf',x,z);
    w.state='stalk'; w.t=9999;
  }
  if(wolves>0){ howlSound(); setTimeout(()=>toast(`🐺 ${wolves} ${wolves>1?'WOLVES':'WOLF'} PROWL THIS WAVE!`),1200); }
  for(let i=0;i<5;i++){ // forest regrows a little each wave
    const s=findSpot(10); if(!s) break;
    if(Math.random()<0.6) pineTree(s.x,s.z,rand(0.8,1.4));
    else leafTree(s.x,s.z,rand(0.8,1.2));
  }
}
let wave=1;
spawnWave(8);

// particles (hit puffs + meat)
const puffs=[];
function puff(p,color=0xaa2222,n=10,size=0.14,grav=9,up=5,life=0.9){
  for(let i=0;i<n;i++){
    const m=new THREE.Mesh(new THREE.BoxGeometry(size,size,size),new THREE.MeshBasicMaterial({color}));
    m.position.copy(p); m.position.y+=0.5;
    m.userData.v=new THREE.Vector3(rand(-3,3),rand(1,up),rand(-3,3));
    m.userData.g=grav;
    m.userData.life=rand(life*0.5,life);
    scene.add(m); puffs.push(m);
  }
}
const _smokeV=new THREE.Vector3(); let smokeT=0;
// ---------- TRACKING: footprints + blood trails ----------
const prints=[];
const printGeo=new THREE.PlaneGeometry(0.14,0.18);
function addPrint(x,z,color,life=20){
  if(prints.length>260){ const old=prints.shift(); scene.remove(old.m); old.m.material.dispose(); }
  const m=new THREE.Mesh(printGeo,new THREE.MeshBasicMaterial({color,transparent:true,opacity:0.35,depthWrite:false}));
  m.rotation.x=-Math.PI/2; m.rotation.z=Math.random()*3;
  m.position.set(x,groundH(x,z)+0.03,z);
  m.renderOrder=1;
  scene.add(m); prints.push({m,life});
}
function updatePrints(dt){
  for(let i=prints.length-1;i>=0;i--){
    const p=prints[i]; p.life-=dt;
    if(p.life<4) p.m.material.opacity=Math.max(0,p.life/4)*0.35;
    if(p.life<=0){ scene.remove(p.m); p.m.material.dispose(); prints.splice(i,1); }
  }
}
let playerPrintT=0;
const meats=[];
function dropMeat(p){
  const gy=groundH(p.x,p.z);
  const m=new THREE.Group(); m.position.set(p.x,gy+0.2,p.z); m.rotation.y=Math.random()*3; scene.add(m);
  box(0.36,0.22,0.3,0xc94a4a,0,0.12,0,m); // meat chunk
  box(0.3,0.07,0.07,0xe8dcc8,0.28,0.14,0,m); // bone sticking out
  box(0.12,0.1,0.12,0xa83333,0,0.26,0,m); // top morsel
  m.userData.bob=0; m.userData.gy=gy; m.userData.isMeat=true; meats.push(m);
}

// ---------- INPUT ----------
const keys={};
addEventListener('keydown',e=>{
  keys[e.code]=true;
  if(e.code==='KeyR'&&!shopOpen){
    if(buildMode){ buildRy+=Math.PI/4; updateGhost(); }
    else if(player.tool==='rod') clearBobber('Reeled in');
    else startReload();
  }
  if(e.code==='KeyV'&&!e.repeat&&started&&!shopOpen&&!buildMode) toggleView();
  if(e.code==='Digit1'&&!e.repeat&&started&&!shopOpen&&!player.dead){ buildMode?selectPiece('wall'):setTool('rifle'); }
  if(e.code==='Digit2'&&!e.repeat&&started&&!shopOpen&&!player.dead){ buildMode?selectPiece('spikes'):setTool('rod'); }
  if(e.code==='Digit3'&&!e.repeat&&started&&!shopOpen&&!player.dead){ buildMode?selectPiece('torch'):setTool('axe'); }
  if(e.code==='KeyB'&&!e.repeat&&started&&!shopOpen&&!player.dead){ buildMode?exitBuild():enterBuild(); }
  if(e.code==='KeyF'&&!e.repeat&&started){
    if(shopOpen) closeShop();
    else if(player.dead){}
    else if(player.sitting) standUp();
    else if(player.aboard) disembark();
    else if(isInsideCabin()) openShop();
    else { const ch=nearChair(); if(ch) sitDown(ch); else if(nearBoat()) boardBoat(); }
  }
  if(e.code==='Escape'&&shopOpen) closeShop();
  else if(e.code==='Escape'&&buildMode) exitBuild();
  if(e.code==='KeyG'&&!e.repeat&&started&&!shopOpen&&!player.dead){
    if(player.meat>0&&player.hp<player.maxHp){
      player.meat--; player.hp=Math.min(player.maxHp,player.hp+30);
      tone(300,150,.2,'sine',.25); toast('🍖 +30 HP');
      refreshMetaHUD(); refreshHp(); saveGame();
    }
    else if(player.fish>0&&player.hp<player.maxHp){
      player.fish--; player.hp=Math.min(player.maxHp,player.hp+20);
      tone(300,150,.2,'sine',.25); toast('🐟 +20 HP');
      refreshMetaHUD(); refreshHp(); saveGame();
    }
    else if(player.meat<1&&player.fish<1) toast('Nothing to eat — hunt or fish!');
  }
  if(['Space','ArrowUp','ArrowDown','Digit1','Digit2','Digit3','KeyB','KeyF','KeyG'].includes(e.code)) e.preventDefault();
});
function sitDown(ch){
  player.sitting=ch;
  if(player.tool!=='rod') setTool('rod');
  tone(400,250,.15,'sine',.2);
  toast('🪑 Sat down — click water to cast');
}
function standUp(){
  const ch=player.sitting; player.sitting=null;
  if(ch){ player.pos.set(ch.x-ch.dx*1.4,0,ch.z-ch.dz*1.4); player.pos.y=groundH(player.pos.x,player.pos.z); }
}
addEventListener('keyup',e=>keys[e.code]=false);
const ray=new THREE.Raycaster();
const mouseNDC=new THREE.Vector2(0,0);
const aimPoint=new THREE.Vector3(3,0,3);
const _aimTarget=new THREE.Vector3(3,0,3);
let _aimInit=true;
let mouseDown=false, rmbHeld=false, clickQueued=false;
addEventListener('mousemove',e=>{
  mouseNDC.x=(e.clientX/innerWidth)*2-1;
  mouseNDC.y=-(e.clientY/innerHeight)*2+1;
  // 1st-person look (pointer-locked) — clamped so a focus loss can't snap you 180°
  if(viewMode==='fps'&&started&&document.pointerLockElement===renderer.domElement){
    const dx=THREE.MathUtils.clamp(e.movementX,-60,60);
    const dy=THREE.MathUtils.clamp(e.movementY,-60,60);
    fpsYaw=wrapAng(fpsYaw-dx*0.0022);
    fpsPitch=THREE.MathUtils.clamp(fpsPitch-dy*0.0022,-1.35,1.35);
  }
});
addEventListener('mousedown',e=>{
  if(e.target.closest('#viewbtn')||e.target.closest('#start')||e.target.closest('#shop')||e.target.closest('#buildbar')) return;
  if(!started||shopOpen) return;
  if(e.button===0){ mouseDown=true; clickQueued=true; }
  if(e.button===2) rmbHeld=true;
});
addEventListener('mouseup',e=>{ if(e.button===2) rmbHeld=false; if(e.button===0) mouseDown=false; });
addEventListener('contextmenu',e=>e.preventDefault());
addEventListener('wheel',e=>{ if(viewMode==='iso'){camZoom=THREE.MathUtils.clamp(camZoom+e.deltaY*0.02,10,42); updateCameraFrustum();} },{passive:true});
renderer.domElement.addEventListener('click',()=>{
  if(viewMode==='fps'&&started&&document.pointerLockElement!==renderer.domElement) renderer.domElement.requestPointerLock?.();
});
document.getElementById('viewbtn').addEventListener('click',e=>{ e.stopPropagation(); if(started) toggleView(); });

function startReload(){
  if(player.reloading||player.ammo===player.magSize) return;
  player.reloading=true;
  toast('RELOADING…');
  setTimeout(()=>{player.ammo=player.magSize;player.reloading=false;updateAmmo();},1100);
}
function toast(msg){
  const el=document.createElement('div'); el.className='tmsg'; el.textContent=msg;
  document.getElementById('toast').appendChild(el);
  setTimeout(()=>el.remove(),3000);
}
function updateAmmo(){
  document.getElementById('ammotxt').textContent=`${player.ammo} / ∞`;
  const w=document.getElementById('ammo'); w.innerHTML='';
  for(let i=0;i<player.magSize;i++){const d=document.createElement('div');d.className='bullet'+(i<player.ammo?'':' empty');w.appendChild(d);}
}
updateAmmo();

// ---------- PROGRESSION: XP, missions, cabin shop, saves ----------
const SAVE_KEY='timberIsleSaveV1';
const COIN_PER_MEAT=6, AMMO_PRICE=10;
const KILL_COINS={rabbit:2,boar:5,deer:8,wolf:10};
const MISSIONS=[
  {id:'m_rabbits', text:'Hunt 2 rabbits', target:2, reward:{xp:30,coins:10}, prog:()=>player.killsByType.rabbit},
  {id:'m_meat', text:'Collect 3 meat 🍖 (walk over red drops)', target:3, reward:{xp:30,coins:20}, prog:()=>player.meatCollected},
  {id:'m_boar', text:'Hunt 2 boar', target:2, reward:{xp:60,coins:25}, prog:()=>player.killsByType.boar},
  {id:'m_level', text:'Reach level 2', target:2, reward:{xp:0,coins:40}, prog:()=>player.level},
  {id:'m_deer', text:'Hunt 2 deer', target:2, reward:{xp:80,coins:30}, prog:()=>player.killsByType.deer},
  {id:'m_sell', text:'Sell meat at the cabin shop (F inside cabin)', target:1, reward:{xp:50,coins:0}, prog:()=>player.soldOnce?1:0},
  {id:'m_wave', text:'Clear 2 waves', target:2, reward:{xp:100,coins:50}, prog:()=>player.wavesCleared},
  {id:'m_wolf', text:'Kill 1 wolf 🐺 (they hunt from wave 2)', target:1, reward:{xp:100,coins:60}, prog:()=>(player.killsByType.wolf||0)},
  {id:'m_wave3', text:'Clear 3 waves', target:3, reward:{xp:150,coins:80}, prog:()=>player.wavesCleared},
  {id:'m_fish', text:'Catch 2 fish 🎣 (rod: press 2)', target:2, reward:{xp:60,coins:30}, prog:()=>player.fishCaught},
  {id:'m_chop', text:'Chop 10 wood 🪵 (axe: press 3)', target:10, reward:{xp:40,coins:20}, prog:()=>player.woodChopped},
  {id:'m_build', text:'Build 3 structures 🔨 (press B)', target:3, reward:{xp:80,coins:40}, prog:()=>player.builtCount},
  {id:'m_boatfish', text:'Catch 2 fish from the boat 🚣', target:2, reward:{xp:100,coins:50}, prog:()=>player.boatFish},
];
let missionIdx=0;
function xpNeed(){ return 100*player.level; }
function refreshMetaHUD(){
  document.getElementById('score').textContent=player.score;
  document.getElementById('kills').textContent=`${player.kills} kills • ${player.meat} meat • ${player.fish} fish • ${player.wood}🪵`;
  document.getElementById('coins').textContent=`🪙 ${player.coins}`;
  document.getElementById('level').textContent=player.level;
  document.getElementById('xpfill').style.width=Math.min(100,player.xp/xpNeed()*100)+'%';
  const rod=player.tool==='rod', axe=player.tool==='axe';
  document.getElementById('wname').textContent=rod?'🎣 ROD':axe?'⛏️ AXE':weapon().name;
  document.getElementById('ammotxt').textContent=rod?'bait ∞':axe?'chop 🌲':`${player.ammo} / ∞`;
}
function updateMissions(){
  while(missionIdx<MISSIONS.length){
    const m=MISSIONS[missionIdx];
    let p=0; try{ p=m.prog(); }catch(e){}
    if(p>=m.target){
      missionIdx++;
      if(m.reward.coins) player.coins+=m.reward.coins;
      missionSound();
      toast(`✅ ${m.text} (+${m.reward.xp}xp +${m.reward.coins}🪙)`);
      refreshMetaHUD();
      if(m.reward.xp) addXP(m.reward.xp);
    } else break;
  }
  const el=document.getElementById('missiontxt');
  const ob=document.getElementById('objective');
  if(missionIdx<MISSIONS.length){
    const m=MISSIONS[missionIdx]; let p=0; try{ p=Math.min(m.prog(),m.target); }catch(e){}
    el.innerHTML=`${m.text} <b>${p}/${m.target}</b>`;
    ob.textContent=`🎯 ${m.text} • wave ${wave}`;
  } else {
    el.innerHTML=`<span class="done">All missions done — free hunt! 🏆</span>`;
    ob.textContent=`🏆 Free hunt • wave ${wave}`;
  }
}
function addXP(n){
  if(n<=0) return;
  player.xp+=n;
  while(player.xp>=xpNeed()){
    player.xp-=xpNeed(); player.level++;
    const bonus=25*player.level; player.coins+=bonus; player.ammo=player.magSize;
    levelupSound();
    toast(`⭐ LEVEL ${player.level}! +${bonus}🪙, ammo refilled`);
    updateAmmo();
  }
  refreshMetaHUD(); updateMissions(); saveGame();
}
function saveGame(){
  try{
    localStorage.setItem(SAVE_KEY,JSON.stringify({
      xp:player.xp,level:player.level,coins:player.coins,meat:player.meat,fish:player.fish,wood:player.wood,score:player.score,kills:player.kills,
      killsByType:player.killsByType,meatCollected:player.meatCollected,fishCaught:player.fishCaught,woodChopped:player.woodChopped,builtCount:player.builtCount,soldOnce:player.soldOnce,wavesCleared:player.wavesCleared,
      weapon:player.weapon,owned:player.owned,missionIdx,boatFish:player.boatFish,
      boat:boat.ok?{x:boat.x,z:boat.z,dir:boat.dir}:null,aboard:!!player.aboard,
      structures:structures.slice(0,80).map(s=>({type:s.type,x:s.x,z:s.z,ry:s.ry||0}))
    }));
  }catch(e){}
}
function loadGame(){
  try{
    const s=JSON.parse(localStorage.getItem(SAVE_KEY));
    if(!s) return false;
    player.xp=s.xp||0; player.level=s.level||1; player.coins=s.coins||0; player.meat=s.meat||0; player.fish=s.fish||0; player.wood=s.wood||0;
    player.score=s.score||0; player.kills=s.kills||0;
    player.killsByType=Object.assign({rabbit:0,boar:0,deer:0,wolf:0},s.killsByType);
    player.meatCollected=s.meatCollected||0; player.fishCaught=s.fishCaught||0; player.boatFish=s.boatFish||0; player.woodChopped=s.woodChopped||0; player.builtCount=s.builtCount||0; player.soldOnce=!!s.soldOnce; player.wavesCleared=s.wavesCleared||0;
    player.hp=100; player.dead=false; player.invulnT=0;
    if(s.weapon&&WEAPONS[s.weapon]) player.weapon=s.weapon;
    if(Array.isArray(s.owned)&&s.owned.length) player.owned=s.owned.filter(id=>WEAPONS[id]);
    if(!player.owned.includes(player.weapon)) player.owned.push(player.weapon);
    missionIdx=s.missionIdx||0;
    player.magSize=weapon().mag; player.ammo=player.magSize;
    if(Array.isArray(s.structures)) for(const sp of s.structures.slice(0,80)){
      try{ if(sp&&PIECES[sp.type]&&isSolid(Math.round(sp.x),Math.round(sp.z))) buildStructure({type:sp.type,x:sp.x,z:sp.z,ry:sp.ry||0}); }catch(e){}
    }
    if(s.boat&&boat.ok){
      boat.x=s.boat.x; boat.z=s.boat.z; boat.dir=s.boat.dir||0;
      boat.mesh.position.set(boat.x,WATER_Y+0.1,boat.z); boat.mesh.rotation.y=boat.dir;
      if(s.aboard){ // wake up ashore next to the boat
        const l=findLanding(boat.x,boat.z)||{x:0,z:8};
        player.pos.set(l.x,0,l.z); player.pos.y=groundH(l.x,l.z);
      }
    }
    player.aboard=null;
    return (player.score>0||player.level>1||player.coins>0||missionIdx>0);
  }catch(e){ return false; }
}
// ----- cabin shop -----
let shopOpen=false;
function isInsideCabin(){
  try{
    const l=cabinLocal(player.pos.x,player.pos.z);
    return Math.abs(l.x)<2.6&&Math.abs(l.z)<2.1;
  }catch(e){ return false; }
}
function openShop(){
  shopOpen=true; renderShop();
  document.getElementById('shop').style.display='flex';
  try{ document.exitPointerLock?.(); }catch(e){}
}
function closeShop(){ shopOpen=false; document.getElementById('shop').style.display='none'; }
function renderShop(){
  document.getElementById('shopbal').innerHTML=`You have <b>${player.meat}🍖</b> meat • <b>${player.fish}🐟</b> fish • <b>${player.wood}🪵</b> wood • <b>${player.coins}🪙</b> coins • <b>Lv ${player.level}</b> • <b>${weapon().name}</b>`;
  const rows=[];
  rows.push(`<div class="row"><div class="t"><b>Sell 1 meat 🍖</b><span>+${COIN_PER_MEAT} coins each</span></div><button class="buy" id="sell1" ${player.meat<1?'disabled':''}>SELL</button></div>`);
  rows.push(`<div class="row"><div class="t"><b>Sell ALL meat (${player.meat}🍖)</b><span>+${player.meat*COIN_PER_MEAT} coins</span></div><button class="buy" id="sellall" ${player.meat<1?'disabled':''}>SELL ALL</button></div>`);
  rows.push(`<div class="row"><div class="t"><b>Sell ALL fish (${player.fish}🐟)</b><span>+${player.fish*8} coins</span></div><button class="buy" id="sellfish" ${player.fish<1?'disabled':''}>SELL</button></div>`);
  rows.push(`<div class="row"><div class="t"><b>Sell ALL wood (${player.wood}🪵)</b><span>+${player.wood*2} coins</span></div><button class="buy" id="sellwood" ${player.wood<1?'disabled':''}>SELL</button></div>`);
  rows.push(`<div class="row"><div class="t"><b>Refill ammo</b><span>${player.ammo}/${player.magSize} → full • ${AMMO_PRICE}🪙</span></div><button class="buy" id="buyammo" ${(player.ammo>=player.magSize||player.coins<AMMO_PRICE)?'disabled':''}>${AMMO_PRICE}🪙</button></div>`);
  for(const id of Object.keys(WEAPONS)){
    const w=WEAPONS[id];
    if(player.owned.includes(id)){
      const eq=player.weapon===id;
      rows.push(`<div class="row"><div class="t"><b>${w.name}</b><span>${w.desc} • dmg ${w.dmg} • mag ${w.mag}</span></div><button class="buy ${eq?'':'eq'}" id="eq_${id}" ${eq?'disabled':''}>${eq?'EQUIPPED':'EQUIP'}</button></div>`);
    } else {
      rows.push(`<div class="row"><div class="t"><b>${w.name}</b><span>${w.desc} • dmg ${w.dmg} • mag ${w.mag}</span></div><button class="buy" id="buy_${id}" ${player.coins<w.price?'disabled':''}>${w.price}🪙</button></div>`);
    }
  }
  document.getElementById('shoprows').innerHTML=rows.join('');
  const on=(id,fn)=>{ const b=document.getElementById(id); if(b) b.onclick=e=>{ e.stopPropagation(); fn(); }; };
  on('sell1',()=>sellMeat(1)); on('sellall',()=>sellMeat(player.meat)); on('sellfish',()=>sellFish()); on('sellwood',()=>sellWood()); on('buyammo',buyAmmo);
  for(const id of Object.keys(WEAPONS)){ on('buy_'+id,()=>buyWeapon(id)); on('eq_'+id,()=>equipWeapon(id)); }
}
function sellMeat(n){
  n=Math.min(n,player.meat); if(n<1) return;
  player.meat-=n; player.coins+=n*COIN_PER_MEAT; player.soldOnce=true;
  coinSound(); toast(`+${n*COIN_PER_MEAT}🪙 for ${n}🍖`);
  refreshMetaHUD(); renderShop(); updateMissions(); saveGame();
}
function sellFish(){
  if(player.fish<1) return;
  player.coins+=player.fish*8; player.soldOnce=true;
  coinSound(); toast(`+${player.fish*8}🪙 for ${player.fish}🐟`);
  player.fish=0;
  refreshMetaHUD(); renderShop(); updateMissions(); saveGame();
}
function sellWood(){
  if(player.wood<1) return;
  player.coins+=player.wood*2; player.soldOnce=true;
  coinSound(); toast(`+${player.wood*2}🪙 for ${player.wood}🪵`);
  player.wood=0;
  refreshMetaHUD(); renderShop(); updateMissions(); saveGame();
}
function buyAmmo(){
  if(player.ammo>=player.magSize||player.coins<AMMO_PRICE) return;
  player.coins-=AMMO_PRICE; player.ammo=player.magSize; player.reloading=false;
  coinSound(); toast('Ammo refilled 🔫'); updateAmmo();
  refreshMetaHUD(); renderShop(); saveGame();
}
function buyWeapon(id){
  const w=WEAPONS[id]; if(!w||player.owned.includes(id)||player.coins<w.price) return;
  player.coins-=w.price; player.owned.push(id); equipWeapon(id);
  coinSound(); toast(`🔫 ${w.name} yours!`);
}
function equipWeapon(id){
  if(!player.owned.includes(id)) return;
  player.weapon=id; player.magSize=WEAPONS[id].mag; player.ammo=player.magSize; player.reloading=false;
  fpsGun.material.color.setHex(id==='sniper'?0x1c1c22:id==='hunter'?0x3a5a2e:0x2e2015);
  toast(`Equipped ${WEAPONS[id].name}`);
  updateAmmo(); refreshMetaHUD(); renderShop(); saveGame();
}
document.getElementById('shopx').onclick=e=>{ e.stopPropagation(); closeShop(); };
document.getElementById('resetsave').onclick=e=>{ e.stopPropagation(); try{localStorage.removeItem(SAVE_KEY);}catch(e){} location.reload(); };
// load previous run (if any)
const hadSave=loadGame();
if(hadSave){
  document.getElementById('playbtn').textContent=`▶ CONTINUE — LV ${player.level} • ${player.coins}🪙 • ${weapon().name}`;
  fpsGun.material.color.setHex(player.weapon==='sniper'?0x1c1c22:player.weapon==='hunter'?0x3a5a2e:0x2e2015);
}
updateAmmo(); refreshMetaHUD(); refreshHp(); updateMissions();
setInterval(()=>{ if(started) saveGame(); },10000);
const shootCooldown={t:0};
function applyHit(best){
  if(!best){
    // dust puff at miss point
    if(viewMode==='iso') puff(aimPoint,0xcbb98a,5);
    return;
  }
  damageAnimal(best,weapon().dmg);
}
// shared damage path: rifle shots AND spike traps
function damageAnimal(a,dmg){
  if(!a||a.dead) return;
  a.hp-=dmg; thudSound();
  puff(a.pos,0xb02020,12);
  // flee!
  animals.forEach(o=>{ if(!o.dead && o.pos.distanceTo(a.pos)<9){o.state='flee';o.t=rand(2,4);o.dir=Math.atan2(o.pos.x-a.pos.x,o.pos.z-a.pos.z)+rand(-0.5,0.5);} });
  if(a.hp>0){ a.bleed=6; return; } // wounded — leaves a blood trail
  a.dead=true; a.deadT=0;
  a.mesh.rotation.z=Math.PI/2; a.mesh.position.y=groundH(a.pos.x,a.pos.z)+0.25;
  puff(a.pos,0x7a1a1a,16);
  player.score+=a.cfg.score; player.kills++; player.meat++;
  player.killsByType[a.type]=(player.killsByType[a.type]||0)+1;
  player.coins+=KILL_COINS[a.type]||0;
  dropMeat(a.pos);
  toast(`+${a.cfg.score} ${a.cfg.name} DOWN! 🍖 +${KILL_COINS[a.type]||0}🪙`);
  refreshMetaHUD();
  const hm=document.getElementById('hitmark'); hm.style.display='block'; setTimeout(()=>hm.style.display='none',180);
  addXP(a.cfg.score);
  checkWave();
  saveGame();
}
function shoot(){
  if(player.tool==='rod'){ if(shootCooldown.t<=0){ shootCooldown.t=0.3; doRodClick(); } return; }
  if(player.tool==='axe'){ if(shootCooldown.t<=0){ shootCooldown.t=0.35; doAxe(); } return; }
  if(buildMode){ if((mouseDown||clickQueued)&&shootCooldown.t<=0){ if(placePiece()) shootCooldown.t=0.3; } clickQueued=false; return; }
  if(shootCooldown.t>0||player.reloading||shopOpen) return;
  if(player.ammo<=0){ startReload(); return; }
  const W=weapon();
  player.ammo--; updateAmmo(); shootCooldown.t=W.cd;
  shotSound();
  if(viewMode==='fps'){
    const gy=groundH(player.pos.x,player.pos.z);
    const eye=new THREE.Vector3(player.pos.x,gy+1.6,player.pos.z);
    const dir=new THREE.Vector3(0,0,-1).applyEuler(new THREE.Euler(fpsPitch,fpsYaw,0,'YXZ'));
    const range=W.range;
    const end=eye.clone().addScaledVector(dir,range);
    // muzzle flash at gun tip (world)
    const tip=new THREE.Vector3(); fpsGunTip.getWorldPosition(tip);
    puff(tip,0xffdd88,3);
    const tg=new THREE.BufferGeometry().setFromPoints([tip,end]);
    const line=new THREE.Line(tg,new THREE.LineBasicMaterial({color:0xfff2a8,transparent:true,opacity:0.9}));
    scene.add(line); setTimeout(()=>scene.remove(line),80);
    // kick
    fpsPitch=Math.min(1.35,fpsPitch+0.025);
    fpsGun.position.z=-0.55; setTimeout(()=>fpsGun.position.z=-0.6,70);
    // 3D ray hit test vs animals (body center follows terrain)
    let best=null,bestD=1e9;
    for(const a of animals){
      if(a.dead) continue;
      const c=new THREE.Vector3(a.pos.x,a.pos.y+0.8*a.cfg.scale+0.4,a.pos.z);
      const to=c.clone().sub(eye);
      const along=to.dot(dir);
      if(along<0.5||along>range) continue;
      const perp=dir.clone().multiplyScalar(along);
      const d=to.sub(perp).length();
      const hitR=0.75*a.cfg.scale+0.35;
      if(d<hitR&&along<bestD){best=a;bestD=along;}
    }
    applyHit(best);
    return;
  }
  // ---- isometric shot ----
  const pgy=groundH(player.pos.x,player.pos.z);
  const agy=groundH(aimPoint.x,aimPoint.z);
  // muzzle flash
  puff(new THREE.Vector3(player.pos.x,pgy+1.2,player.pos.z),0xffdd88,4);
  // direction player->aim
  const dir=new THREE.Vector3().subVectors(aimPoint,player.pos); dir.y=0; dir.normalize();
  // tracer
  const len=Math.hypot(aimPoint.x-player.pos.x,aimPoint.z-player.pos.z);
  const tg=new THREE.BufferGeometry().setFromPoints([new THREE.Vector3(player.pos.x,pgy+1.2,player.pos.z),new THREE.Vector3(aimPoint.x,agy+0.6,aimPoint.z)]);
  const line=new THREE.Line(tg,new THREE.LineBasicMaterial({color:0xfff2a8,transparent:true,opacity:0.9}));
  scene.add(line); setTimeout(()=>scene.remove(line),80);
  // recoil + camera kick
  hunter.position.y-=0.05;
  // hit test: nearest animal within 1.1m of shot line, within range
  let best=null,bestD=1e9;
  const origin=new THREE.Vector3(player.pos.x,pgy+0.8,player.pos.z);
  animals.forEach(a=>{
    if(a.dead) return;
    const to=new THREE.Vector3().subVectors(a.pos,origin); to.y=0;
    const along=to.dot(dir);
    if(along<0||along>len+2) return;
    const perp=new THREE.Vector3().copy(dir).multiplyScalar(along);
    const d=new THREE.Vector3().subVectors(to,perp).length();
    const hitR=0.7*a.cfg.scale+0.35;
    if(d<hitR && along<bestD){best=a;bestD=along;}
  });
  applyHit(best);
}
function checkWave(){
  const left=animals.filter(a=>!a.dead).length;
  document.getElementById('left').textContent=`${left} left`;
  if(left===0){
    wave++; player.wavesCleared++;
    document.getElementById('wavenum').textContent=wave;
    toast(`WAVE ${wave} — more game coming in!`);
    updateMissions(); saveGame();
    setTimeout(()=>spawnWave(3+wave),1500);
  }
}

// ---------- SURVIVAL: bites, death, respawn ----------
function refreshHp(){
  const f=document.getElementById('hpfill');
  if(!f) return;
  f.style.width=Math.max(0,player.hp/player.maxHp*100)+'%';
  f.classList.toggle('low',player.hp<30);
}
function hurtPlayer(dmg,from){
  if(player.dead||player.invulnT>0||!started) return;
  player.hp-=dmg; player.lastHurtT=performance.now()/1000; player.shakeT=0.3;
  thudSound();
  puff(new THREE.Vector3(player.pos.x,player.pos.y+1.2,player.pos.z),0xb02020,8);
  const d=document.getElementById('dmg'); d.style.opacity=0.9; setTimeout(()=>{d.style.opacity=0;},220);
  if(viewMode==='fps'){ fpsPitch=Math.min(1.35,fpsPitch+0.07); fpsYaw+=rand(-0.05,0.05); }
  refreshHp();
  if(player.hp<=0){ player.hp=0; refreshHp(); gameOver(); }
}
function gameOver(){
  player.dead=true;
  try{ document.exitPointerLock?.(); }catch(e){}
  document.getElementById('gostats').innerHTML=`Score <b>${player.score}</b> • Kills <b>${player.kills}</b> • Wave <b>${wave}</b> • Level <b>${player.level}</b>`;
  document.getElementById('gameover').style.display='flex';
  saveGame();
}
function respawn(){
  player.dead=false; player.hp=player.maxHp; player.invulnT=3;
  player.pos.set(0,groundH(0,8),8); player.pos.y=groundH(0,8);
  player.ammo=player.magSize; player.reloading=false;
  const fee=Math.floor(player.coins*0.1); player.coins-=fee;
  animals.forEach(a=>{ if(!a.dead&&a.type==='wolf'&&Math.hypot(a.pos.x-player.pos.x,a.pos.z-player.pos.z)<20){a.state='flee';a.t=4;a.dir=Math.atan2(a.pos.x-player.pos.x,a.pos.z-player.pos.z);} });
  document.getElementById('gameover').style.display='none';
  refreshHp(); refreshMetaHUD(); updateAmmo(); saveGame();
  toast('🩹 Patched up — 3s protection');
}
document.getElementById('respawnbtn').onclick=e=>{ e.stopPropagation(); respawn(); };

// ---------- FISHING: cast, nibbles, strike, catch ----------
const FISH_TYPES=[
  {name:'Sunny Perch',pts:10},
  {name:'Mud Catfish',pts:25},
  {name:'Golden Koi',pts:60},
];
const fish={state:'idle',waitT:0,waitTotal:1,biteT:0,castT:0,from:new THREE.Vector3(),to:new THREE.Vector3(),nib1:false,nib2:false,elapsed:0};
const bobber=new THREE.Group();
box(0.12,0.12,0.12,0xd93b2b,0,0.1,0,bobber);
box(0.12,0.12,0.12,0xf2ede0,0,-0.02,0,bobber);
box(0.03,0.25,0.03,0x222222,0,0.25,0,bobber);
bobber.visible=false; scene.add(bobber);
const ripple=new THREE.Mesh(new THREE.RingGeometry(0.3,0.42,24),new THREE.MeshBasicMaterial({color:0xffffff,transparent:true,opacity:0.7,side:THREE.DoubleSide}));
ripple.rotation.x=-Math.PI/2; ripple.visible=false; scene.add(ripple);
const fishLineGeo=new THREE.BufferGeometry().setFromPoints([new THREE.Vector3(),new THREE.Vector3()]);
const fishLine=new THREE.Line(fishLineGeo,new THREE.LineBasicMaterial({color:0xeeeeee,transparent:true,opacity:0.8}));
fishLine.visible=false; fishLine.frustumCulled=false; scene.add(fishLine);
const _tipV=new THREE.Vector3();
function waterAt(x,z){
  if(Math.abs(x)>WORLD_R+2||Math.abs(z)>WORLD_R+2) return false;
  return !isSolid(Math.round(x),Math.round(z));
}
function rodTipWorld(){
  rodG.updateWorldMatrix(true,false);
  return _tipV.copy(rodTipLocal).applyMatrix4(rodG.matrixWorld);
}
function doRodClick(){
  if(player.tool!=='rod'||player.dead||shopOpen) return;
  if(fish.state==='idle'){
    let tx,tz,ok=false;
    if(viewMode==='fps'){
      const eye=new THREE.Vector3(player.pos.x,player.pos.y+1.6,player.pos.z);
      const dir=new THREE.Vector3(0,0,-1).applyEuler(new THREE.Euler(fpsPitch,fpsYaw,0,'YXZ'));
      for(let d=2;d<40;d+=0.75){
        const px=eye.x+dir.x*d, pz=eye.z+dir.z*d;
        if(waterAt(px,pz)){ tx=px; tz=pz; ok=true; break; }
      }
      if(!ok){ toast('🎣 Aim at open water to cast'); return; }
    } else {
      tx=aimPoint.x; tz=aimPoint.z;
      const dd=Math.hypot(tx-player.pos.x,tz-player.pos.z);
      if(!waterAt(tx,tz)){ toast('🎣 Aim at blue water to cast'); return; }
      if(dd>16||dd<1.5){ toast('🎣 Too far — get closer to shore'); return; }
    }
    fish.from.copy(rodTipWorld()); fish.to.set(tx,WATER_Y+0.05,tz);
    fish.castT=0; fish.state='cast'; fishLine.visible=true;
    tone(300,900,.25,'sine',.15);
  }
  else if(fish.state==='waiting'){ clearBobber('Reeled in — too soon!'); }
  else if(fish.state==='bite'){ strikeCatch(); }
}
function clearBobber(msg){
  fish.state='idle';
  bobber.visible=false; ripple.visible=false; fishLine.visible=false;
  try{ document.getElementById('biteprompt').style.display='none'; }catch(e){}
  if(msg) toast(msg);
}
function strikeCatch(){
  const sitting=!!(player.sitting||player.aboard);
  const r=Math.random()+(sitting?0.12:0)+(isDark()?0.05:0);
  const f=r<0.55?FISH_TYPES[0]:r<0.85?FISH_TYPES[1]:FISH_TYPES[2];
  player.fish++; player.fishCaught++;
  if(player.aboard) player.boatFish++;
  player.score+=f.pts;
  puff(bobber.position,0xbfe0ea,10,0.12,9,4,0.8);
  splashSound(); coinSound();
  toast(`🐟 ${f.name}! +${f.pts}`);
  clearBobber();
  refreshMetaHUD(); addXP(f.pts); updateMissions(); saveGame();
}
function updateFishing(dt){
  if(fish.state==='idle') return;
  const tip=rodTipWorld();
  const pos=fishLineGeo.attributes.position.array;
  pos[0]=tip.x;pos[1]=tip.y;pos[2]=tip.z;
  pos[3]=bobber.position.x;pos[4]=bobber.position.y;pos[5]=bobber.position.z;
  fishLineGeo.attributes.position.needsUpdate=true;
  if(fish.state==='cast'){
    fish.castT+=dt/0.6;
    const t=Math.min(1,fish.castT);
    bobber.visible=true;
    bobber.position.lerpVectors(fish.from,fish.to,t);
    bobber.position.y+=Math.sin(t*Math.PI)*3;
    if(t>=1){
      const sitting=!!(player.sitting||player.aboard);
      fish.waitTotal=rand(3,8)*(sitting?0.7:1);
      fish.waitT=fish.waitTotal; fish.elapsed=0; fish.nib1=false; fish.nib2=false;
      fish.state='waiting';
      ripple.visible=true; ripple.position.copy(fish.to); ripple.position.y+=0.02;
      splashSound();
      puff(fish.to,0xbfe0ea,8,0.12,9,4,0.7);
    }
  }
  else if(fish.state==='waiting'){
    fish.waitT-=dt; fish.elapsed+=dt;
    const frac=1-fish.waitT/fish.waitTotal;
    bobber.position.y=WATER_Y+0.05+Math.sin(performance.now()*0.004)*0.05;
    bobber.position.x=fish.to.x+Math.sin(performance.now()*0.001)*0.1;
    const rp=(performance.now()*0.001)%1;
    ripple.scale.setScalar(1+rp*2);
    ripple.material.opacity=0.7*(1-rp);
    if(!fish.nib1&&frac>0.55){ fish.nib1=true; bobber.position.y-=0.12; tone(500,300,.1,'sine',.15); }
    if(!fish.nib2&&frac>0.8){ fish.nib2=true; bobber.position.y-=0.12; tone(500,300,.1,'sine',.15); }
    if(fish.waitT<=0){
      fish.state='bite'; fish.biteT=(player.sitting||player.aboard)?1.6:1.2;
      splashSound(); plipSound();
      puff(fish.to,0xffffff,10,0.12,9,4,0.7);
      document.getElementById('biteprompt').style.display='block';
    }
  }
  else if(fish.state==='bite'){
    fish.biteT-=dt;
    bobber.position.y=WATER_Y-0.22+Math.sin(performance.now()*0.03)*0.05;
    if(fish.biteT<=0){ clearBobber('💨 It got away...'); }
  }
}

// ---------- LUMBER: chop, shake, fell, stumps ----------
const falling=[], stumps=[];
function doAxe(){
  if(player.tool!=='axe'||player.dead||shopOpen||buildMode) return;
  swingT=0.25;
  if(viewMode==='fps') fpsAxe.rotation.x=-1.1;
  let best=null,bd=2.9, bestStruct=null,sd=2.9;
  for(const t of fadeTrees){
    if(t.op<0.5) continue;
    const d=Math.hypot(t.x-player.pos.x,t.z-player.pos.z);
    if(d<bd){ bd=d; best=t; }
  }
  for(const s of structures){
    const d=Math.hypot(s.x-player.pos.x,s.z-player.pos.z);
    if(d<sd){ sd=d; bestStruct=s; }
  }
  if(bestStruct&&(!best||sd<=bd)){ chopStructure(bestStruct); return; }
  if(!best){ tone(200,120,.08,'square',.12); return; }
  best.hp=(best.hp===undefined?3:best.hp)-1;
  best.shake=0.3;
  player.wood++; player.woodChopped++;
  thunkSound();
  puff(new THREE.Vector3(best.x,groundH(best.x,best.z)+1.2,best.z),0x8a6238,6,0.12,9,4,0.6);
  refreshMetaHUD(); updateMissions(); saveGame();
  if(typeof renderBuildBar==='function') renderBuildBar();
  if(best.hp<=0) fellTree(best);
}
function chopStructure(s){
  s.hp=(s.hp===undefined?(s.type==='wall'?3:2):s.hp)-1;
  thunkSound();
  puff(new THREE.Vector3(s.x,groundH(s.x,s.z)+1,s.z),0xc9a86a,6,0.12,9,4,0.6);
  if(s.hp>0){ toast(`🔨 ${PIECES[s.type].name} damaged (${s.hp} left)`); return; }
  // demolished — salvage half the cost
  scene.remove(s.mesh);
  s.mesh.traverse(o=>{ if(o.geometry) o.geometry.dispose(); if(o.material&&o.material.dispose) o.material.dispose(); });
  const si=structures.indexOf(s); if(si>=0) structures.splice(si,1);
  if(s.tref){ const ti=torches.indexOf(s.tref); if(ti>=0) torches.splice(ti,1); }
  const refund=Math.floor(PIECES[s.type].cost/2);
  player.wood+=refund;
  crashSound();
  toast(`🧱 Demolished — +${refund}🪵 salvaged`);
  refreshMetaHUD(); saveGame();
  if(typeof renderBuildBar==='function') renderBuildBar();
}
function fellTree(t){
  t.shake=0; t.g.rotation.z=0; t.falling=true;
  const ci=treeColliders.indexOf(t.col); if(ci>=0) treeColliders.splice(ci,1);
  falling.push({t,dir:Math.random()<0.5?1:-1,k:0});
  crashSound();
}
function updateLumber(dt){
  for(const t of fadeTrees){
    if(t.shake>0){
      t.shake-=dt;
      if(t.shake<=0){ t.shake=0; t.g.rotation.z=0; }
      else t.g.rotation.z=Math.sin(performance.now()*0.05)*0.12*t.shake*3;
    }
  }
  for(let i=falling.length-1;i>=0;i--){
    const f=falling[i]; f.k+=dt/0.7;
    f.t.g.rotation.z=f.dir*Math.min(1,f.k)*1.5;
    if(f.k>=1){
      const t=f.t;
      scene.remove(t.g);
      t.g.traverse(o=>{ if(o.geometry) o.geometry.dispose(); if(o.material&&o.material.dispose) o.material.dispose(); });
      const fi=fadeTrees.indexOf(t); if(fi>=0) fadeTrees.splice(fi,1);
      const gy=groundH(t.x,t.z);
      const st=new THREE.Group(); st.position.set(t.x,gy,t.z); scene.add(st);
      box(0.45,0.35,0.45,0x5a3a1e,0,0.17,0,st);
      box(0.47,0.06,0.47,0x8a6a42,0,0.36,0,st);
      stumps.push(st);
      if(stumps.length>40){ const old=stumps.shift(); scene.remove(old); }
      player.wood+=2;
      puff(new THREE.Vector3(t.x,gy+0.5,t.z),0x5da24a,12,0.14,9,5,1);
      refreshMetaHUD(); saveGame();
      falling.splice(i,1);
    }
  }
}

// ---------- UPDATE ----------
const clock=new THREE.Clock();
let started=false;
document.getElementById('start').addEventListener('click',()=>{
  document.getElementById('start').style.display='none';
  started=true; audio();
});

// (collision helpers live above, next to VEGETATION, so world gen can use them)
// dollhouse cutaway — call every frame
function updateTreeFade(dt){
  // iso only: trees near the hunter pop out entirely and pop back,
  // exactly like the cabin roof cutaway (with hysteresis so edges don't flicker)
  if(viewMode!=='iso'){
    for(const t of fadeTrees){ if(!t.g.visible){ t.g.visible=true; t.op=1; if(t.col) t.col.hidden=false; } }
    return;
  }
  const hx=player.pos.x, hz=player.pos.z;
  const axeMode=player.tool==='axe';
  for(const t of fadeTrees){
    if(t.falling) continue;
    // axe out = nothing hides, so you can see (and hit) what you chop
    if(axeMode){ if(t.op<0.5){ t.op=1; t.g.visible=true; if(t.col) t.col.hidden=false; } continue; }
    const ddx=t.x-hx, ddz=t.z-hz, d2=ddx*ddx+ddz*ddz;
    if(t.op>0.5&&d2<7*7){ t.op=0; t.g.visible=false; if(t.col) t.col.hidden=true; }
    else if(t.op<0.5&&d2>8*8){ t.op=1; t.g.visible=true; if(t.col) t.col.hidden=false; }
  }
}
function updateCabinCutaway(dt){
  const l=cabinLocal(player.pos.x,player.pos.z);
  const inside=Math.abs(l.x)<2.6&&Math.abs(l.z)<2.1;
  const nearCabin=Math.hypot(player.pos.x-cabin.position.x,player.pos.z-cabin.position.z)<8;
  // roof: fade + lift when inside (iso only — in FPS you want the roof over your head)
  const roofTarget=(inside&&viewMode!=='fps')?0:1;
  roofOp+=(roofTarget-roofOp)*Math.min(1,dt*5);
  cabinRoof.visible=roofOp>0.04;
  cabinRoof.position.y=(1-roofOp)*3.2;
  for(const m of roofMats) m.opacity=roofOp;
  // chimney + cap ride with the roof group now (no ghost stack)
  winNorth.material.opacity=cabinWalls[0].op;
  winWest.material.opacity=cabinWalls[5].op;
  // walls facing the camera fade when you're near/inside
  const activeCam = viewMode==='fps'?fpsCam:camera;
  const cx=activeCam.position.x-cabin.position.x, cz=activeCam.position.z-cabin.position.z;
  const cl=Math.hypot(cx,cz)||1; const cdx=cx/cl, cdz=cz/cl;
  for(const w of cabinWalls){
    // world normal = R * local normal
    const wnX=w.n[0]*_cabCos+w.n[2]*_cabSin, wnZ=-w.n[0]*_cabSin+w.n[2]*_cabCos;
    const facing=wnX*cdx+wnZ*cdz; // >0 means wall is between camera and interior
    let target=1;
    if(viewMode!=='fps'&&nearCabin&&facing>0.15) target=0.15;
    if(viewMode!=='fps'&&inside&&facing>0.15) target=0.12;
    w.op+=(target-w.op)*Math.min(1,dt*6);
    w.m.material.opacity=w.op;
    w.m.castShadow=w.op>0.5;
  }
  if(inside&&!wasInside){
    toast('🏠 Cabin trader — press F');
  }
  wasInside=inside;
}

function update(dt){
  let ml=0;
  // trade prompt (hidden while shop open)
  try{
    const sp=document.getElementById('shopprompt');
    if(!shopOpen&&started&&!player.dead){
      if(player.sitting){ sp.style.display='block'; sp.innerHTML=fish.state==='idle'?'🎣 Click water to cast — <b>F</b> to stand':'🎣 ... <b>F</b> to stand'; }
      else if(player.aboard){ sp.style.display='block'; sp.innerHTML='🚣 WASD paddle • Click to fish • <b>F</b> ashore'; }
      else if(isInsideCabin()){ sp.style.display='block'; sp.innerHTML='🏠 Press <b>F</b> to trade'; }
      else if(nearChair()){ sp.style.display='block'; sp.innerHTML='🪑 Press <b>F</b> to sit & fish'; }
      else if(nearBoat()){ sp.style.display='block'; sp.innerHTML='🚣 Press <b>F</b> to board'; }
      else sp.style.display='none';
    } else sp.style.display='none';
  }catch(e){}
  if(shopOpen){ shootCooldown.t=Math.max(0,shootCooldown.t-dt); updateCabinCutaway(dt); return; }
  if(player.dead){ updateCabinCutaway(dt); return; }
  updateDaylight(dt);
  updateWeather(dt);
  if(viewMode==='fps'){
    // ----- 1ST PERSON: mouse look (pointer lock), WASD relative to look -----
    const fwdX=-Math.sin(fpsYaw), fwdZ=-Math.cos(fpsYaw);
    const rightX=Math.cos(fpsYaw), rightZ=-Math.sin(fpsYaw);
    const sprint=(keys['ShiftLeft']||keys['ShiftRight'])?1.7:1;
    let mx=0,mz=0;
    if(keys['KeyW']||keys['ArrowUp']){mx+=fwdX;mz+=fwdZ;}
    if(keys['KeyS']||keys['ArrowDown']){mx-=fwdX;mz-=fwdZ;}
    if(keys['KeyD']||keys['ArrowRight']){mx+=rightX;mz+=rightZ;}
    if(keys['KeyA']||keys['ArrowLeft']){mx-=rightX;mz-=rightZ;}
    ml=Math.hypot(mx,mz);
    if(ml>0){mx/=ml;mz/=ml;}
    if(player.sitting) ml=0; // seated: no walking
    const sp=player.speed*sprint;
    if(player.aboard){ if(ml>0) moveBoat(mx,mz,dt); }
    else if(ml>0){
      const tryX=player.pos.x+mx*sp*dt;
      if(stepOK(player.pos.x,player.pos.z,tryX,player.pos.z)) player.pos.x=tryX;
      const tryZ=player.pos.z+mz*sp*dt;
      if(stepOK(player.pos.x,player.pos.z,player.pos.x,tryZ)) player.pos.z=tryZ;
    }
    if(!player.aboard){
      // stick to terrain (smooth steps)
      const fgy=groundH(player.pos.x,player.pos.z);
      player.pos.y+=(fgy-player.pos.y)*Math.min(1,dt*12);
    }
    // keep hunter synced (hidden) so iso mode resumes facing right way
    hunter.position.copy(player.pos);
    hunter.rotation.y=wrapAng(fpsYaw+Math.PI);
    // eye camera + viewmodel bob
    fpsCam.position.set(player.pos.x,player.pos.y+1.65+(ml>0?Math.sin(performance.now()*0.012)*0.05:0),player.pos.z);
    fpsCam.rotation.set(0,0,0);
    fpsCam.rotation.order='YXZ';
    fpsCam.rotation.y=fpsYaw; fpsCam.rotation.x=fpsPitch;
    // sniper scope: hold RMB in FPS; binoculars: hold C anywhere
    let wantFov=75;
    if(player.weapon==='sniper'&&rmbHeld) wantFov=22;
    else if(keys['KeyC']) wantFov=30;
    if(Math.abs(fpsCam.fov-wantFov)>0.3){ fpsCam.fov+=(wantFov-fpsCam.fov)*Math.min(1,dt*10); fpsCam.updateProjectionMatrix(); }
    updateTreeFade(dt); // restores any iso-faded trees
  } else {
    // ----- ISOMETRIC (current): mouse ring aim, W = gun forward -----
    // smoothed aim vs terrain-height plane: prevents ring snap when rotating camera (Q/E) or on resizes
    ray.setFromCamera(mouseNDC,camera);
    const planeY=player.pos.y;
    const t=(planeY-ray.ray.origin.y)/ray.ray.direction.y;
    if(t>0&&t<300){
      _aimTarget.copy(ray.ray.origin).addScaledVector(ray.ray.direction,t);
      _aimTarget.x=THREE.MathUtils.clamp(_aimTarget.x,player.pos.x-16,player.pos.x+16);
      _aimTarget.z=THREE.MathUtils.clamp(_aimTarget.z,player.pos.z-16,player.pos.z+16);
      _aimTarget.y=groundH(_aimTarget.x,_aimTarget.z);
      // snap on first frames, glide after (no sudden jumps)
      if(_aimInit){ aimPoint.copy(_aimTarget); _aimInit=false; }
      else aimPoint.lerp(_aimTarget,1-Math.exp(-14*dt));
    }
    aimRing.position.set(aimPoint.x,aimPoint.y+0.06,aimPoint.z);
    aimDot.position.set(aimPoint.x,aimPoint.y+0.07,aimPoint.z);
    const pring=1+Math.sin(performance.now()*0.006)*0.06; aimRing.scale.set(pring,pring,1);

    // camera rotate + binocular scout zoom (hold C)
    if(keys['KeyQ']) camAzim+=dt*1.6;
    if(keys['KeyE']) camAzim-=dt*1.6;
    viewZoom+=(((keys['KeyC']?10:camZoom))-viewZoom)*Math.min(1,dt*6);
    {
      const asp=innerWidth/innerHeight;
      camera.left=-viewZoom*asp/2; camera.right=viewZoom*asp/2;
      camera.top=viewZoom/2; camera.bottom=-viewZoom/2;
      camera.updateProjectionMatrix();
    }

    // player move — GUN-RELATIVE: W = where gun points (aim), S = back, A/D = strafe
    const face0=Math.atan2(aimPoint.x-player.pos.x,aimPoint.z-player.pos.z);
    const fwdX=Math.sin(face0), fwdZ=Math.cos(face0);
    const rightX=-Math.cos(face0), rightZ=Math.sin(face0);
    const sprint=(keys['ShiftLeft']||keys['ShiftRight'])?1.7:1;
    let mx=0,mz=0;
    if(keys['KeyW']||keys['ArrowUp']){mx+=fwdX;mz+=fwdZ;}
    if(keys['KeyS']||keys['ArrowDown']){mx-=fwdX;mz-=fwdZ;}
    if(keys['KeyD']||keys['ArrowRight']){mx+=rightX;mz+=rightZ;}
    if(keys['KeyA']||keys['ArrowLeft']){mx-=rightX;mz-=rightZ;}
    ml=Math.hypot(mx,mz);
    if(ml>0){mx/=ml;mz/=ml;}
    if(player.sitting) ml=0; // seated: no walking
    const sp=player.speed*sprint;
    // axis-separated slide so you never get stuck in trees: try X then Z (slope-aware)
    if(player.aboard){ if(ml>0) moveBoat(mx,mz,dt); }
    else if(ml>0){
      const tryX=player.pos.x+mx*sp*dt;
      if(stepOK(player.pos.x,player.pos.z,tryX,player.pos.z)) player.pos.x=tryX;
      const tryZ=player.pos.z+mz*sp*dt;
      if(stepOK(player.pos.x,player.pos.z,player.pos.x,tryZ)) player.pos.z=tryZ;
    }
    if(!player.aboard){
      const pgy2=groundH(player.pos.x,player.pos.z);
      player.pos.y+=(pgy2-player.pos.y)*Math.min(1,dt*12);
    }
    // face aim — gun leads, fast + smooth (wrap-safe, no 360 snap)
    const face=Math.atan2(aimPoint.x-player.pos.x,aimPoint.z-player.pos.z);
    hunter.position.copy(player.pos);
    hunter.rotation.y=wrapAng(hunter.rotation.y+angDelta(face,hunter.rotation.y)*Math.min(1,dt*14));
    // walk cycle: big leg stride, working left arm, steady gun arm, roll + bounce + dust
    const wob=performance.now()*0.012, sw=ml>0?Math.sin(wob)*0.95:0;
    hunterParts.legL.rotation.x=sw; hunterParts.legR.rotation.x=-sw;
    hunterParts.armL.rotation.x=hunterParts.armL.userData.base+sw*0.45;
    hunterParts.armR.rotation.x=hunterParts.armR.userData.base-sw*0.15;
    hunter.rotation.z=ml>0?Math.sin(wob)*0.06:0;
    hunter.position.y = player.pos.y+(ml>0 ? Math.abs(Math.cos(wob))*0.11 : 0);
    const tb=ml>0?-Math.abs(Math.cos(wob))*0.05:0;
    rifle.position.y=0.95+tb; rodG.position.y=1.05+tb; axeG.position.y=0.95+tb;
    if(ml>0){
      stepT-=dt;
      if(stepT<=0){
        stepT=(keys['ShiftLeft']||keys['ShiftRight'])?0.2:0.3;
        puff(new THREE.Vector3(player.pos.x+rand(-0.2,0.2),player.pos.y+0.1,player.pos.z+rand(-0.2,0.2)),0xcbb98a,2,0.1,9,2,0.5);
      }
    }
  }

  if(player.sitting){
    // perched on the chair: sink to seat, legs forward, rod ready
    const ch=player.sitting;
    hunter.position.set(ch.x,ch.gy-0.28,ch.z);
    hunter.rotation.y=ch.ry;
    hunterParts.legL.rotation.x=-1.5; hunterParts.legR.rotation.x=-1.5;
    hunterParts.armL.rotation.x=-0.6; hunterParts.armR.rotation.x=-0.8;
    if(viewMode==='fps') fpsCam.position.set(ch.x,ch.gy+1.05,ch.z);
  }
  if(player.aboard){
    // riding the skiff: planted on the mid bench, rowing when moving
    hunter.position.set(player.pos.x,WATER_Y+0.05,player.pos.z);
    hunter.rotation.y=boat.dir;
    hunterParts.legL.rotation.x=-1.2; hunterParts.legR.rotation.x=-1.2;
    const row=ml>0?Math.sin(performance.now()*0.008)*0.5:0;
    hunterParts.armL.rotation.x=-0.7+row; hunterParts.armR.rotation.x=-0.7-row;
    if(boat.oarL) boat.oarL.rotation.x=row;
    if(boat.oarR) boat.oarR.rotation.x=-row;
    if(viewMode==='fps') fpsCam.position.set(player.pos.x,WATER_Y+1.45,player.pos.z);
  }

  updateFishing(dt);

  if(buildMode) updateGhost();

  if(buildMode){ if(mouseDown||clickQueued) shoot(); clickQueued=false; }
  else if(clickQueued){ clickQueued=false; shoot(); }
  shootCooldown.t=Math.max(0,shootCooldown.t-dt);
  if(swingT>0){ swingT-=dt; if(swingT<=0&&viewMode==='fps') fpsAxe.rotation.x=-0.05; }
  updateLumber(dt);
  // scout vignette
  try{
    const scoping=viewMode==='fps'?((player.weapon==='sniper'&&rmbHeld)||keys['KeyC']):!!keys['KeyC'];
    document.getElementById('scopevig').style.opacity=scoping?1:0;
  }catch(e){}
  if(ml>0&&!player.dead&&!player.aboard){
    playerPrintT-=dt;
    if(playerPrintT<=0){ playerPrintT=0.35; addPrint(player.pos.x+rand(-0.2,0.2),player.pos.z+rand(-0.2,0.2),0x6e5a40,12); }
  }

  // animals AI
  for(let ai=animals.length-1;ai>=0;ai--){
    const a=animals[ai];
    if(a.dead){
      a.deadT+=dt;
      if(a.deadT>15){ // corpse cleanup — meat drops stay
        puff(a.pos,0x999988,6);
        scene.remove(a.mesh);
        a.mesh.traverse(o=>{ if(o.geometry) o.geometry.dispose(); if(o.material&&o.material.dispose) o.material.dispose(); });
        animals.splice(ai,1);
      }
      continue;
    }
    a.phase+=dt*6; a.t-=dt;
    a.biteCD=Math.max(0,(a.biteCD||0)-dt);
    if(a.bleed>0){ // dripping blood trail while fleeing wounded
      a.bleed-=dt; a.bloodT-=dt;
      if(a.bloodT<=0){ a.bloodT=0.5; puff(a.pos,0xc01818,3,0.12,9,3,0.7); }
    }
    const dp=Math.hypot(a.pos.x-player.pos.x,a.pos.z-player.pos.z);
    if(a.type==='wolf'&&!player.dead){
      // wounded wolves break off; healthy ones stalk you — bolder + faster at night/fog
      const bold=isDark(), range=bold?40:26;
      if(a.hp<a.maxHp&&a.state!=='flee'){a.state='flee';a.t=2.5;a.dir=Math.atan2(a.pos.x-player.pos.x,a.pos.z-player.pos.z);}
      else if(a.hp>=a.maxHp&&a.state==='flee'&&a.t<=0){a.state='stalk';a.t=9999;}
      if(a.state==='stalk'||(a.state!=='flee'&&a.state!=='dead'&&dp<range&&a.hp>=a.maxHp)){
        a.state='stalk';
        a.dir=Math.atan2(player.pos.x-a.pos.x,player.pos.z-a.pos.z);
        a.speed=a.cfg.speed*(bold?1.15:1);
        if(dp<1.8){ a.speed=0; if(a.biteCD<=0){ a.biteCD=0.9; hurtPlayer(12,a); } }
      }
    }
    else if(a.type==='wolf'&&player.dead){ if(a.state==='stalk'){a.state='wander';a.t=rand(2,4);} }
    else if(dp<(weather.type==='rain'?9:6)&&a.state!=='flee'){a.state='flee';a.t=rand(2,3.5);a.dir=Math.atan2(a.pos.x-player.pos.x,a.pos.z-player.pos.z);}
    if(a.state==='graze'){
      a.speed=0;
      a.mesh.rotation.y+=Math.sin(a.phase*0.2)*dt*0.5;
      if(a.t<=0){a.state='wander';a.t=rand(2,5);a.dir=rand(0,Math.PI*2);}
    } else if(a.state==='wander'){
      a.speed=a.cfg.speed*0.35;
      if(a.t<=0){a.state='graze';a.t=rand(1.5,4);}
      if(Math.random()<dt*0.3) a.dir+=rand(-0.6,0.6);
      if(a.herd&&!a.herd.dead){ // trail the herd leader
        const hx=a.herd.pos.x-a.pos.x, hz=a.herd.pos.z-a.pos.z;
        if(Math.hypot(hx,hz)>6) a.dir=Math.atan2(hx,hz)+rand(-0.2,0.2);
      }
    } else if(a.state==='flee'){
      a.speed=a.cfg.speed;
      if(a.t<=0){ if(a.type==='wolf'&&!player.dead&&a.hp>=a.maxHp){a.state='stalk';a.t=9999;} else {a.state='wander';a.t=rand(2,4);} }
    } else if(a.state==='stalk'){
      a.speed=a.cfg.speed; // movement handled below with live direction
    }
    if(a.speed>0){
      const vx=Math.sin(a.dir)*a.speed*dt, vz=Math.cos(a.dir)*a.speed*dt;
      const ox=a.pos.x, oz=a.pos.z, nx=ox+vx, nz=oz+vz;
      if(stepOK(ox,oz,nx,nz,0.4)){ a.pos.x=nx; a.pos.z=nz; a.blockT=0; }
      else {
        // slide along trunks instead of stopping dead
        let slid=false;
        if(stepOK(ox,oz,nx,oz,0.4)){ a.pos.x=nx; slid=true; }
        if(stepOK(a.pos.x,oz,a.pos.x,nz,0.4)){ a.pos.z=nz; slid=true; }
        if(slid){ a.blockT=0; }
        else {
          if(a.turnSide===undefined) a.turnSide=Math.random()<0.5?1:-1;
          a.dir+=a.turnSide*3.2*dt;
          a.blockT=(a.blockT||0)+dt;
          if(a.blockT>1.2){ a.dir+=Math.PI; a.blockT=0; a.turnSide*=-1; } // fully wedged: about-face
        }
      }
      const want=Math.atan2(vx,vz);
      a.mesh.rotation.y=wrapAng(a.mesh.rotation.y+angDelta(want,a.mesh.rotation.y)*Math.min(1,dt*8));
    }
    if(!a.dead) a.pos.y+=(groundH(a.pos.x,a.pos.z)-a.pos.y)*Math.min(1,dt*10);
    if(a.speed>0.3){ // footprints while moving
      a.printT-=dt;
      if(a.printT<=0){ a.printT=0.5; addPrint(a.pos.x,a.pos.z,a.type==='wolf'?0x2c2c33:0x4a3a28); }
    }
    if(!a.dead&&structures.length){ // spike traps
      a.spikeCD=Math.max(0,(a.spikeCD||0)-dt);
      if(a.spikeCD<=0){
        for(const s of structures){
          if(s.type!=='spikes') continue;
          if(Math.hypot(a.pos.x-s.x,a.pos.z-s.z)<1.1){
            a.spikeCD=1.5;
            puff(a.pos,0xb02020,8);
            damageAnimal(a,2);
            break;
          }
        }
      }
    }
    // leg gallop (pivot swing) + graze head bob
    a.parts.legs.forEach((l,i)=>{ l.rotation.x=Math.sin(a.phase+(i%2)*Math.PI)*(a.speed>0.1?0.6:0.04); });
    if(a.state==='graze') a.parts.head.position.y=a.parts.headY+Math.sin(a.phase*0.5)*0.08-0.15*a.cfg.scale;
    else a.parts.head.position.y+=(a.parts.headY-a.parts.head.position.y)*dt*5;
  }

  // meat pickup (walk over)
  for(let i=meats.length-1;i>=0;i--){
    const m=meats[i]; m.userData.bob+=dt;
    m.position.y=m.userData.gy+0.25+Math.sin(m.userData.bob*4)*0.06; m.rotation.y+=dt*2;
    if(Math.hypot(m.position.x-player.pos.x,m.position.z-player.pos.z)<1){
      scene.remove(m); meats.splice(i,1);
      player.score+=5; player.meat++; player.meatCollected++;
      toast('+5 MEAT COLLECTED 🍖'); thudSound();
      refreshMetaHUD(); addXP(5); saveGame();
    }
  }
  // puffs
  for(let i=puffs.length-1;i>=0;i--){
    const p=puffs[i]; p.userData.life-=dt;
    p.position.addScaledVector(p.userData.v,dt); p.userData.v.y-=(p.userData.g===undefined?9:p.userData.g)*dt;
    p.scale.multiplyScalar(1-dt*1.2);
    if(p.userData.life<=0){scene.remove(p);puffs.splice(i,1);}
  }
  updatePrints(dt);
  // ambient: blades, flame, water, chimney smoke
  blades.rotation.z+=dt*1.8;
  flame.scale.set(1,1+Math.sin(performance.now()*0.02)*0.15,1);
  flameIn.scale.set(1,1+Math.cos(performance.now()*0.027)*0.2,1);
  smokeT-=dt;
  if(smokeT<=0&&roofOp>0.5){
    smokeT=0.4;
    chimney.getWorldPosition(_smokeV); _smokeV.y+=0.9;
    puff(_smokeV,Math.random()<0.5?0xbbbbbb:0x999999,1,0.3,-1.2,0.8,2.4);
  }
  water.position.y=WATER_Y+Math.sin(performance.now()*0.001)*0.12;
  checkWaveThrottled();

  // survival: invuln tick, regen, cabin heal
  player.invulnT=Math.max(0,player.invulnT-dt);
  if(player.hp<player.maxHp){
    if(isInsideCabin()){ player.hp=Math.min(player.maxHp,player.hp+15*dt); refreshHp(); }
    else if(performance.now()/1000-player.lastHurtT>5){ player.hp=Math.min(player.maxHp,player.hp+2*dt); refreshHp(); }
  }
  if(player.shakeT>0) player.shakeT-=dt;
  // camera follow — lerped so view toggles / Q-E spins glide instead of snapping
  if(viewMode==='fps'){
    // fpsCam already positioned in movement branch
    if(player.shakeT>0){ fpsCam.position.x+=rand(-1,1)*player.shakeT*0.6; fpsCam.position.y+=rand(-1,1)*player.shakeT*0.6; }
  } else {
    const cx=player.pos.x+Math.sin(camAzim)*camDist*0.7;
    const cz=player.pos.z+Math.cos(camAzim)*camDist*0.7;
    const cy=player.pos.y+camDist*0.75;
    _camTarget.set(cx,cy,cz);
    if(_camInit){ camera.position.copy(_camTarget); _camInit=false; }
    else camera.position.lerp(_camTarget,1-Math.exp(-7*dt));
    if(player.shakeT>0){ camera.position.x+=rand(-1,1)*player.shakeT*1.2; camera.position.y+=rand(-1,1)*player.shakeT*1.2; }
    camera.lookAt(player.pos.x,player.pos.y,player.pos.z);
    updateTreeFade(dt);
  }
  updateMinimap(dt); updateCompass();
  updateCabinCutaway(dt);
}
let lastCheck=0;
function checkWaveThrottled(){
  const now=performance.now();
  if(now-lastCheck<500) return; lastCheck=now;
  checkWave();
}

function loop(){
  requestAnimationFrame(loop);
  const dt=Math.min(clock.getDelta(),0.05);
  if(started) update(dt);
  else {
    // idle orbit behind start card
    const t=performance.now()*0.0002;
    camera.position.set(Math.sin(t)*30,24,Math.cos(t)*30);
    camera.lookAt(0,0,0);
    blades.rotation.z+=dt*1.8;
  }
  renderer.render(scene,viewMode==='fps'&&started?fpsCam:camera);
}
loop();
</script>
</body>
</html>
