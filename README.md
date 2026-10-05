<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1">
<title>VERB MOVE CHALLENGE</title>
<!-- MediaPipe Pose (CDN). Butuh internet saat pertama dibuka. -->
<script src="https://cdn.jsdelivr.net/npm/@mediapipe/pose@0.5.1675469404/pose.js" crossorigin="anonymous"></script>
<style>
:root{--bg1:#1b1464;--bg2:#5b21b6;--blue:#2563eb;--red:#e11d48;--yellow:#fde047;--green:#22c55e;--ink:#fff}
*{box-sizing:border-box;margin:0}
html,body{height:100%;overflow:hidden;font-family:"Trebuchet MS","Segoe UI",Arial,sans-serif;color:var(--ink);background:linear-gradient(135deg,var(--bg1),var(--bg2))}
/* Stage 16:9 yang menyesuaikan layar */
#stage{position:fixed;left:50%;top:50%;width:100vw;height:56.25vw;max-height:100vh;max-width:177.78vh;transform:translate(-50%,-50%);font-size:min(1vw,1.7778vh)}
.screen{position:absolute;inset:0;display:none;flex-direction:column;align-items:center;justify-content:center;text-align:center;padding:3em}
.screen.on{display:flex}
h1{font-size:7em;line-height:1;text-shadow:0 .1em 0 rgba(0,0,0,.3)}
h2{font-size:4em}
.sub{font-size:3em;color:var(--yellow);margin:.4em 0 .8em;letter-spacing:.05em}
.chips{display:flex;gap:2em;font-size:2.2em;margin-bottom:1.2em}
.chips span{background:rgba(255,255,255,.14);padding:.4em .9em;border-radius:1em}
.btn{font:inherit;font-size:2.6em;font-weight:800;color:#2a1a00;background:var(--yellow);border:0;border-radius:.6em;padding:.45em 1.4em;margin:.3em;cursor:pointer;box-shadow:0 .15em 0 #b59b00,0 0 1.2em rgba(253,224,71,.6);transition:transform .1s}
.btn:hover{transform:scale(1.05)}.btn:active{transform:translateY(.08em)}
.btn.alt{background:#fff;box-shadow:0 .15em 0 #aaa}
.btn.sm{font-size:1.6em}
.small{font-size:1.5em;opacity:.85;margin-top:.6em}
/* Camera check */
#camwrap{position:relative;width:48em;height:27em;border-radius:1.2em;overflow:hidden;background:#000;border:.25em solid #fff;margin:1em 0}
#camwrap.mini{position:absolute;right:1.5em;bottom:1.5em;width:20em;height:11.25em;margin:0;z-index:5;border-width:.15em}
video,#overlay{position:absolute;inset:0;width:100%;height:100%;transform:scaleX(-1);object-fit:cover}
.zones{position:absolute;inset:0;display:flex;pointer-events:none}
.zones div{flex:1;border-right:.1em dashed rgba(255,255,255,.6);display:flex;align-items:flex-start;justify-content:center;font-size:1.4em;padding-top:.3em;font-weight:700}
.zones div:nth-child(1){flex:40;background:rgba(37,99,235,.18)}
.zones div:nth-child(2){flex:20}
.zones div:nth-child(3){flex:40;background:rgba(225,29,72,.18);border:0}
#status{position:absolute;left:.6em;top:.6em;background:rgba(0,0,0,.65);padding:.3em .7em;border-radius:.6em;font-size:1.4em;font-weight:700;z-index:2}
#camwrap.mini #status{font-size:1em}
#posebig{font-size:4.5em;font-weight:900;min-height:1.2em;color:var(--yellow)}
.pop{animation:pop .25s}
@keyframes pop{from{transform:scale(1.4)}to{transform:scale(1)}}
/* Game */
#top{position:absolute;left:0;right:0;top:0;display:flex;justify-content:space-between;align-items:center;padding:1.2em 2em;font-size:2.6em;font-weight:900}
#top .pill{background:rgba(0,0,0,.3);border-radius:.7em;padding:.2em .8em}
#score{font-size:1.25em;color:var(--yellow)}
#combo{position:absolute;top:5.6em;left:50%;transform:translateX(-50%);font-size:3em;font-weight:900;color:#fb923c;opacity:0;transition:opacity .2s}
#bar{position:absolute;top:5.4em;left:2em;right:2em;height:1em;background:rgba(0,0,0,.35);border-radius:1em;overflow:hidden}
#barfill{height:100%;width:100%;background:var(--green);transition:width .1s linear,background .3s}
#qbox{position:absolute;top:8.5em;left:4em;right:4em;height:21em;display:flex;align-items:center;justify-content:center;text-align:center;font-size:5.4em;font-weight:900;line-height:1.15}
#answers{position:absolute;left:0;right:0;bottom:0;height:41%;display:flex}
.ans{flex:1;margin:0 1.2em 1.2em;border-radius:1.2em;display:flex;flex-direction:column;align-items:center;justify-content:center;font-size:6em;font-weight:900;transition:transform .2s,filter .2s,box-shadow .2s;border:.06em solid rgba(255,255,255,.5)}
.ans small{font-size:.5em;opacity:.9}
#ansA{background:linear-gradient(160deg,#3b82f6,#1d4ed8)}
#ansB{background:linear-gradient(160deg,#fb7185,#be123c)}
.ans.hover{transform:scale(1.04);box-shadow:0 0 2em 1em rgba(253,224,71,.8)}
.ans.dim{filter:brightness(.45)}
.ans.right{box-shadow:0 0 2.5em 1em rgba(34,197,94,.95);filter:none;transform:scale(1.04)}
#hint{position:absolute;left:0;right:0;bottom:44%;text-align:center;font-size:3em;font-weight:900;color:var(--yellow);text-shadow:0 .08em .2em #000;min-height:1.2em}
#lockbar{height:.5em;width:0;background:var(--yellow);border-radius:1em;margin:0 auto}
#fb{position:absolute;inset:0;display:none;align-items:center;justify-content:center;flex-direction:column;font-size:9em;font-weight:900;background:rgba(0,0,0,.45);z-index:8;text-shadow:0 .05em .2em #000;text-align:center}
#fb small{font-size:.3em;color:var(--yellow)}
.shake{animation:shake .5s}
@keyframes shake{20%{transform:translateX(-3%)}40%{transform:translateX(3%)}60%{transform:translateX(-2%)}80%{transform:translateX(2%)}}
#conf{position:absolute;inset:0;pointer-events:none;z-index:9}
/* Modal & teacher */
.modal{position:absolute;inset:0;background:rgba(10,5,40,.88);display:none;align-items:center;justify-content:center;flex-direction:column;z-index:20;padding:3em}
.modal.on{display:flex}
.card{background:#fff;color:#1b1464;border-radius:1.2em;padding:1.6em 2.4em;text-align:left;font-size:2em;max-width:40em;width:100%}
.card h2{font-size:1.6em;margin-bottom:.4em}
.card p{margin:.4em 0}
.row{display:flex;justify-content:space-between;align-items:center;margin:.4em 0;font-size:.9em}
.row input[type=number]{width:4em;font-size:1em;padding:.1em;border-radius:.3em;border:.1em solid #888}
.row input[type=checkbox]{width:1.2em;height:1.2em}
#topbtns{position:absolute;right:1.5em;top:1.2em;display:flex;gap:.6em;z-index:12}
.stat{font-size:3em;margin:.2em 0}
</style>
</head>
<body>
<div id="stage">

  <div id="topbtns">
    <button class="btn alt sm" id="btnSound">🔊 SOUND ON</button>
    <button class="btn alt sm" id="btnTeacher">⚙️ TEACHER</button>
  </div>

  <!-- START -->
  <div class="screen on" id="sStart">
    <div class="small">🎮 VERB MOVE CHALLENGE</div>
    <h1>REGULAR &amp;<br>IRREGULAR VERBS</h1>
    <div class="sub">MOVE • THINK • ANSWER!</div>
    <div class="chips"><span id="chipQ">🎯 20 Questions</span><span id="chipT">⏱️ 10 Seconds</span><span>🏆 Get the Highest Score!</span></div>
    <div><button class="btn" id="btnStart">START GAME</button><button class="btn alt" id="btnHow">HOW TO PLAY</button></div>
  </div>

  <!-- CAMERA CHECK -->
  <div class="screen" id="sCam">
    <h2>📷 CAMERA CHECK</h2>
    <div class="small">Get Ready! Stand in front of the camera.</div>
    <div id="camwrap"><!-- video & overlay dipindah ke sini oleh JS --></div>
    <div id="posebig">⬆️ CENTER</div>
    <div id="camFail" style="display:none">
      <h2>CAMERA NOT AVAILABLE</h2>
      <div class="small">Camera access is required for movement-based gameplay.</div>
    </div>
    <div>
      <button class="btn" id="btnCamGo">LET'S GO!</button>
      <button class="btn alt" id="btnDemo">DEMO MODE (← →)</button>
    </div>
  </div>

  <!-- GAME -->
  <div class="screen" id="sGame">
    <div id="top"><div class="pill" id="qnum">Q 01/20</div><div class="pill">SCORE: <span id="score">000</span></div><div class="pill" id="timer">10s</div></div>
    <div id="bar"><div id="barfill"></div></div>
    <div id="combo"></div>
    <div id="qbox"><span id="qtext"></span></div>
    <div id="hint"></div>
    <div id="answers">
      <div class="ans" id="ansA"><span id="txtA"></span><small>⬅️</small></div>
      <div class="ans" id="ansB"><span id="txtB"></span><small>➡️</small></div>
    </div>
    <div id="fb"></div>
  </div>

  <!-- RESULT -->
  <div class="screen" id="sEnd">
    <h1 style="font-size:5.5em">🎉 GAME COMPLETE!</h1>
    <div class="small">YOUR SCORE</div>
    <div style="font-size:8em;font-weight:900;color:var(--yellow)" id="rScore">0</div>
    <div class="stat">CORRECT: <b id="rC"></b> &nbsp; WRONG: <b id="rW"></b> &nbsp; ACCURACY: <b id="rA"></b></div>
    <div style="font-size:4.5em;font-weight:900" id="rMsg"></div>
    <div><button class="btn" id="btnAgain">PLAY AGAIN</button><button class="btn alt" id="btnMenu">BACK TO MENU</button></div>
  </div>

  <canvas id="conf"></canvas>

  <!-- HOW TO PLAY -->
  <div class="modal" id="mHow"><div class="card">
    <h2>HOW TO PLAY</h2>
    <p>1️⃣ Stand in front of the camera.</p><p>2️⃣ Look at the two answers.</p>
    <p>3️⃣ Move your body to the LEFT or RIGHT.</p><p>4️⃣ Stay in position until your answer is detected.</p>
    <p>5️⃣ Get points for every correct answer.</p><p>6️⃣ Try to get the highest score!</p>
    <div style="text-align:center"><button class="btn" id="btnHowOk">LET'S PLAY!</button></div>
  </div></div>

  <!-- TEACHER SETTINGS -->
  <div class="modal" id="mTeach"><div class="card">
    <h2>TEACHER SETTINGS</h2>
    <div class="row">Number of Questions (max <span id="maxQ"></span>)<input type="number" id="setN" min="1" value="20"></div>
    <div class="row">Timer (seconds)<input type="number" id="setT" min="3" max="60" value="10"></div>
    <div class="row">Sound ON<input type="checkbox" id="setSnd" checked></div>
    <div class="row">Camera ON<input type="checkbox" id="setCam" checked></div>
    <div class="row">Demo Mode (keyboard)<input type="checkbox" id="setDemo"></div>
    <div class="row">Random Questions<input type="checkbox" id="setRnd" checked></div>
    <div style="text-align:center"><button class="btn sm" id="btnTeachOk">SAVE</button><button class="btn alt sm" id="btnReset">RESET GAME</button></div>
  </div></div>
</div>

<script>
/* ============================================================
   DATA SOAL — guru bisa mengganti / menambah soal di sini.
   Format: {question, answerA, answerB, correct:"A" atau "B"}
   Posisi jawaban benar akan diacak otomatis saat game berjalan.
   ============================================================ */
const questions = [
 {question:'What is the past form of "GO"?', answerA:"WENT", answerB:"GOED", correct:"A"},
 {question:'What is the past form of "PLAY"?', answerA:"PLAYED", answerB:"PLAY", correct:"A"},
 {question:'What is the past form of "EAT"?', answerA:"EATED", answerB:"ATE", correct:"B"},
 {question:'What is the past form of "SEE"?', answerA:"SAW", answerB:"SEED", correct:"A"},
 {question:'What is the past form of "TAKE"?', answerA:"TAKED", answerB:"TOOK", correct:"B"},
 {question:'What is the past form of "STUDY"?', answerA:"STUDIED", answerB:"STUDYED", correct:"A"},
 {question:'What is the past form of "BUY"?', answerA:"BUYED", answerB:"BOUGHT", correct:"B"},
 {question:'What is the past form of "WATCH"?', answerA:"WATCHED", answerB:"WATCHT", correct:"A"},
 {question:'What is the past form of "BEGIN"?', answerA:"BEGAN", answerB:"BEGINNED", correct:"A"},
 {question:'What is the past form of "SPEAK"?', answerA:"SPEAKED", answerB:"SPOKE", correct:"B"},
 {question:'What is the base form of "WROTE"?', answerA:"WRITE", answerB:"WRITED", correct:"A"},
 {question:'What is the base form of "CAME"?', answerA:"COMEN", answerB:"COME", correct:"B"},
 {question:'What is the base form of "DRANK"?', answerA:"DRINK", answerB:"DRANKE", correct:"A"},
 {question:'What is the base form of "CLEANED"?', answerA:"CLEANE", answerB:"CLEAN", correct:"B"},
 {question:'She ____ to school yesterday.', answerA:"GO", answerB:"WENT", correct:"B"},
 {question:'He ____ his homework last night.', answerA:"HELPED", answerB:"HELP", correct:"A"},
 {question:'Which verb is IRREGULAR?', answerA:"OPENED", answerB:"RAN", correct:"B"},
 {question:'Which verb is REGULAR?', answerA:"VISITED", answerB:"GAVE", correct:"A"},
 {question:'Which verb is IRREGULAR?', answerA:"MADE", answerB:"CALLED", correct:"A"},
 {question:'We ____ a big dinner last Sunday.', answerA:"HAVED", answerB:"HAD", correct:"B"},
 {question:'Choose the correct verb: I ____ a new phone.', answerA:"GOT", answerB:"GETTED", correct:"A"},
 {question:'They ____ the car yesterday.', answerA:"WASHED", answerB:"WASH", correct:"A"}
];

/* ================= STATE & SETTINGS ================= */
const $ = id => document.getElementById(id);
const S = {n:20, time:10, sound:true, cam:true, demo:false, rnd:true};
let G = null;          // state permainan
let camReady = false, poseObj = null, stream = null, camLoopOn = false;
let curZone = 'CENTER', playerSeen = false, lastSeen = 0, smoothX = 0.5;

function show(id){document.querySelectorAll('.screen').forEach(e=>e.classList.remove('on'));$(id).classList.add('on');}
function shuffle(a){a=a.slice();for(let i=a.length-1;i>0;i--){const j=Math.floor(Math.random()*(i+1));[a[i],a[j]]=[a[j],a[i]];}return a;}
function pad(n){return String(n).padStart(3,'0');}

/* ================= SOUND (Web Audio, tanpa file) ================= */
let AC=null;
function tone(f,d,type='sine',v=.2,delay=0){
  if(!S.sound)return;
  try{AC=AC||new (window.AudioContext||window.webkitAudioContext)();
    const o=AC.createOscillator(),g=AC.createGain();o.type=type;o.frequency.value=f;
    const t=AC.currentTime+delay;g.gain.setValueAtTime(v,t);g.gain.exponentialRampToValueAtTime(.001,t+d);
    o.connect(g);g.connect(AC.destination);o.start(t);o.stop(t+d);}catch(e){}
}
const sfx={
  start:()=>[523,659,784,1047].forEach((f,i)=>tone(f,.2,'square',.15,i*.1)),
  tick:()=>tone(880,.08,'square',.1),
  ok:()=>[659,880,1175].forEach((f,i)=>tone(f,.18,'triangle',.25,i*.09)),
  bad:()=>{tone(200,.3,'sawtooth',.2);tone(140,.4,'sawtooth',.2,.15)},
  up:()=>{tone(300,.5,'sawtooth',.2);tone(220,.6,'sawtooth',.2,.3)},
  combo:()=>[784,988,1319].forEach((f,i)=>tone(f,.12,'square',.15,i*.07)),
  end:()=>[523,659,784,659,784,1047].forEach((f,i)=>tone(f,.25,'triangle',.25,i*.14))
};

/* ================= CONFETTI ================= */
const cv=$('conf'),cx=cv.getContext('2d');let parts=[],confOn=false;
function confetti(){
  cv.width=cv.clientWidth;cv.height=cv.clientHeight;
  for(let i=0;i<140;i++)parts.push({x:cv.width/2,y:cv.height*.45,vx:(Math.random()-.5)*28,vy:-Math.random()*26-4,s:8+Math.random()*10,c:`hsl(${Math.random()*360},90%,60%)`,r:Math.random()*6});
  if(!confOn){confOn=true;requestAnimationFrame(confLoop);}
}
function confLoop(){
  cx.clearRect(0,0,cv.width,cv.height);
  parts.forEach(p=>{p.vy+=.7;p.x+=p.vx;p.y+=p.vy;p.r+=.2;cx.fillStyle=p.c;cx.save();cx.translate(p.x,p.y);cx.rotate(p.r);cx.fillRect(-p.s/2,-p.s/2,p.s,p.s*.6);cx.restore();});
  parts=parts.filter(p=>p.y<cv.height+20);
  if(parts.length)requestAnimationFrame(confLoop);else{confOn=false;cx.clearRect(0,0,cv.width,cv.height);}
}

/* ================= CAMERA + POSE ================= */
const video=document.createElement('video');video.playsInline=true;video.muted=true;
const overlay=document.createElement('canvas');overlay.id='overlay';
const zones=document.createElement('div');zones.className='zones';
zones.innerHTML='<div>LEFT ZONE</div><div>CENTER</div><div>RIGHT ZONE</div>';
const status=document.createElement('div');status.id='status';status.textContent='🔴 PLAYER NOT DETECTED';
const cw=$('camwrap');cw.append(video,overlay,zones,status);

async function startCamera(){
  if(camReady)return true;
  try{
    stream=await navigator.mediaDevices.getUserMedia({video:{width:640,height:360},audio:false});
    video.srcObject=stream;await video.play();
    if(typeof Pose==='undefined')throw new Error('MediaPipe tidak termuat (cek internet)');
    poseObj=new Pose({locateFile:f=>`https://cdn.jsdelivr.net/npm/@mediapipe/pose@0.5.1675469404/${f}`});
    poseObj.setOptions({modelComplexity:0,smoothLandmarks:true,minDetectionConfidence:.5,minTrackingConfidence:.5});
    poseObj.onResults(onPose);
    camReady=true;loopCam();return true;
  }catch(e){console.warn('Camera error:',e);return false;}
}
async function loopCam(){
  if(!camReady)return;
  if(video.readyState>=2){try{await poseObj.send({image:video});}catch(e){}}
  requestAnimationFrame(loopCam);
}
/* Titik acuan: rata-rata hidung, bahu, dan pinggul -> posisi tubuh (0..1).
   Dirata-ratakan (smoothing) agar kepala/tangan bergerak tidak mengubah jawaban. */
function onPose(r){
  overlay.width=video.videoWidth||640;overlay.height=video.videoHeight||360;
  const c=overlay.getContext('2d');c.clearRect(0,0,overlay.width,overlay.height);
  const L=r.poseLandmarks;
  if(!L){playerSeen=false;updateStatus();return;}
  const pts=[0,11,12,23,24].map(i=>L[i]).filter(p=>p&&(p.visibility===undefined||p.visibility>.4));
  if(pts.length<2){playerSeen=false;updateStatus();return;}
  const raw=1-(pts.reduce((a,p)=>a+p.x,0)/pts.length); // 1- karena kamera dicerminkan
  smoothX=smoothX*.7+raw*.3;
  playerSeen=true;lastSeen=performance.now();
  // gambar titik tubuh
  c.fillStyle='#fde047';[0,11,12,23,24].forEach(i=>{const p=L[i];c.beginPath();c.arc(p.x*overlay.width,p.y*overlay.height,6,0,7);c.fill();});
  setZone(smoothX<.4?'LEFT':smoothX>.6?'RIGHT':'CENTER');
  updateStatus();
}
function setZone(z){
  if(z!==curZone){curZone=z;const e=$('posebig');e.textContent=z==='LEFT'?'⬅️ LEFT':z==='RIGHT'?'RIGHT ➡️':'⬆️ CENTER';e.classList.remove('pop');void e.offsetWidth;e.classList.add('pop');}
}
function updateStatus(){status.textContent=playerSeen?'🟢 TRACKING — PLAYER DETECTED':'🔴 PLAYER NOT DETECTED';}
function moveCam(mini){ // pindahkan preview ke pojok saat bermain
  const target=mini?$('sGame'):$('sCam');
  if(mini){cw.classList.add('mini');$('sGame').appendChild(cw);}else{cw.classList.remove('mini');$('sCam').insertBefore(cw,$('posebig'));}
}

/* ================= GAME LOGIC ================= */
function buildQuiz(){
  let pool=S.rnd?shuffle(questions):questions.slice();
  pool=pool.slice(0,Math.min(S.n,questions.length));
  // acak posisi jawaban benar tiap soal
  return pool.map(q=>{
    const correctText=q.correct==='A'?q.answerA:q.answerB, wrongText=q.correct==='A'?q.answerB:q.answerA;
    const left=Math.random()<.5;
    return {question:q.question,A:left?correctText:wrongText,B:left?wrongText:correctText,correct:left?'A':'B'};
  });
}
function startGame(){
  G={quiz:buildQuiz(),i:0,score:0,combo:0,right:0,wrong:0,locked:true,left:S.time,timerId:null,hold:null,holdStart:0,pending:null};
  sfx.start();show('sGame');
  if(S.cam&&camReady&&!S.demo)moveCam(true);else cw.style.display='none';
  nextQ();
}
function nextQ(){
  if(G.i>=G.quiz.length)return endGame();
  const q=G.quiz[G.i];
  $('qnum').textContent=`Q ${String(G.i+1).padStart(2,'0')}/${String(G.quiz.length).padStart(2,'0')}`;
  $('qtext').textContent=q.question;$('txtA').textContent='A. '+q.A;$('txtB').textContent='B. '+q.B;
  ['ansA','ansB'].forEach(id=>$(id).className='ans');
  $('hint').innerHTML=S.demo||!camReady?'Press ⬅️ or ➡️':'Move your body LEFT or RIGHT!';
  $('fb').style.display='none';G.left=S.time;G.locked=false;G.hold=null;G.cool=performance.now()+800; // jeda awal agar tidak langsung terkunci
  drawTimer();clearInterval(G.timerId);
  G.timerId=setInterval(()=>{
    if(G.locked)return;G.left-=.1;drawTimer();
    if(G.left<=3&&Math.abs(G.left-Math.round(G.left))<.05)sfx.tick();
    if(G.left<=0)timeUp();
  },100);
  requestAnimationFrame(detectLoop);
}
function drawTimer(){
  const t=Math.max(0,G.left);$('timer').textContent=Math.ceil(t)+'s';
  const f=$('barfill');f.style.width=(t/S.time*100)+'%';f.style.background=t/S.time>.5?'#22c55e':t/S.time>.25?'#facc15':'#ef4444';
}
/* Deteksi: posisi harus bertahan di LEFT/RIGHT selama 0.8 detik sebelum dikunci */
const HOLD_MS=800;
function detectLoop(){
  if(!G||G.locked||!sGame.classList.contains('on'))return;
  const now=performance.now();let z=null;
  if(!S.demo&&camReady&&playerSeen&&now-lastSeen<1000)z=curZone;
  if(now<G.cool)z=null;
  if(z==='LEFT'||z==='RIGHT'){
    if(G.hold!==z){G.hold=z;G.holdStart=now;sfx.tick();}
    const p=Math.min(1,(now-G.holdStart)/HOLD_MS);
    $('hint').innerHTML=`MOVE ${z}! ${z==='LEFT'?'⬅️':'➡️'}<div id="lockbar" style="width:${p*30}em"></div>`;
    $('ansA').classList.toggle('hover',z==='LEFT');$('ansB').classList.toggle('hover',z==='RIGHT');
    if(p>=1)return lockAnswer(z==='LEFT'?'A':'B');
  }else{
    if(G.hold){G.hold=null;$('ansA').classList.remove('hover');$('ansB').classList.remove('hover');}
    if(!S.demo&&camReady&&(!playerSeen||now-lastSeen>1000))$('hint').textContent='🔴 Please stand in front of the camera.';
    else if(!S.demo&&camReady)$('hint').textContent='Move your body LEFT or RIGHT!';
  }
  requestAnimationFrame(detectLoop);
}
// Demo mode / cadangan: keyboard
document.addEventListener('keydown',e=>{
  if(!G||G.locked||!sGame.classList.contains('on'))return;
  if(!(S.demo||!camReady))return;
  if(e.key==='ArrowLeft')lockAnswer('A');else if(e.key==='ArrowRight')lockAnswer('B');
});
function lockAnswer(choice){
  if(G.locked)return;G.locked=true;clearInterval(G.timerId); // hanya satu jawaban per soal
  const q=G.quiz[G.i];const ok=choice===q.correct;
  $('ansA').classList.remove('hover');$('ansB').classList.remove('hover');
  $('ans'+q.correct).classList.add('right');$('ans'+(q.correct==='A'?'B':'A')).classList.add('dim');
  const fb=$('fb');fb.style.display='flex';
  if(ok){
    G.right++;G.combo++;G.score+=100;sfx.ok();confetti();
    if(G.combo>=2){sfx.combo();$('combo').textContent='🔥 COMBO x'+G.combo;$('combo').style.opacity=1;}
    fb.innerHTML='🎉 CORRECT!';$('score').textContent=pad(G.score);
  }else{
    G.wrong++;G.combo=0;sfx.bad();$('combo').style.opacity=0;$('sGame').classList.add('shake');
    setTimeout(()=>$('sGame').classList.remove('shake'),500);
    fb.innerHTML='❌ WRONG!<small>Correct: '+q.correct+'. '+q[q.correct]+'</small>';
  }
  fb.style.color=ok?'#86efac':'#fca5a5';
  setTimeout(()=>{G.i++;nextQ();},1800);
}
function timeUp(){
  G.locked=true;clearInterval(G.timerId);G.combo=0;G.wrong++;sfx.up();$('combo').style.opacity=0;
  const q=G.quiz[G.i];$('ans'+q.correct).classList.add('right');$('ans'+(q.correct==='A'?'B':'A')).classList.add('dim');
  const fb=$('fb');fb.style.display='flex';fb.style.color='#fde047';
  fb.innerHTML="⏰ TIME'S UP!<small>Correct: "+q.correct+'. '+q[q.correct]+'</small>';
  setTimeout(()=>{G.i++;nextQ();},2000);
}
function endGame(){
  clearInterval(G.timerId);cw.style.display='';
  if(cw.classList.contains('mini'))moveCam(false);
  const n=G.quiz.length,acc=Math.round(G.right/n*100);
  $('rScore').textContent=G.score.toLocaleString('en-US');$('rC').textContent=G.right+'/'+n;$('rW').textContent=G.wrong+'/'+n;$('rA').textContent=acc+'%';
  $('rMsg').textContent=acc>=90?'🏆 VERB MASTER!':acc>=75?'🌟 GREAT JOB!':acc>=60?'👍 GOOD TRY!':'💪 KEEP PRACTICING!';
  show('sEnd');sfx.end();confetti();G=null;
}

/* ================= UI WIRING ================= */
function refreshChips(){$('chipQ').textContent='🎯 '+Math.min(S.n,questions.length)+' Questions';$('chipT').textContent='⏱️ '+S.time+' Seconds';$('btnSound').textContent=S.sound?'🔊 SOUND ON':'🔇 SOUND OFF';}
$('maxQ').textContent=questions.length;
$('btnSound').onclick=()=>{S.sound=!S.sound;$('setSnd').checked=S.sound;refreshChips();};
$('btnHow').onclick=()=>$('mHow').classList.add('on');
$('btnHowOk').onclick=()=>{$('mHow').classList.remove('on');$('btnStart').click();};
$('btnTeacher').onclick=()=>$('mTeach').classList.add('on');
$('btnTeachOk').onclick=()=>{
  S.n=Math.max(1,Math.min(questions.length,+$('setN').value||20));S.time=Math.max(3,Math.min(60,+$('setT').value||10));
  S.sound=$('setSnd').checked;S.cam=$('setCam').checked;S.demo=$('setDemo').checked;S.rnd=$('setRnd').checked;
  refreshChips();$('mTeach').classList.remove('on');
};
$('btnReset').onclick=()=>{
  if(G){clearInterval(G.timerId);G=null;}
  S.n=20;S.time=10;S.sound=true;S.cam=true;S.demo=false;S.rnd=true;
  $('setN').value=20;$('setT').value=10;$('setSnd').checked=true;$('setCam').checked=true;$('setDemo').checked=false;$('setRnd').checked=true;
  cw.style.display='';if(cw.classList.contains('mini'))moveCam(false);
  refreshChips();$('mTeach').classList.remove('on');show('sStart');
};
$('btnStart').onclick=async()=>{
  AC=AC||null;sfx.start();
  if(S.demo||!S.cam){$('camFail').style.display='none';return startGame();}
  show('sCam');cw.style.display='';
  const ok=await startCamera();
  $('camFail').style.display=ok?'none':'block';cw.style.display=ok?'':'none';$('posebig').style.display=ok?'':'none';
  $('btnCamGo').style.display=ok?'':'none';
};
$('btnCamGo').onclick=startGame;
$('btnDemo').onclick=()=>{S.demo=true;$('setDemo').checked=true;$('camFail').style.display='none';startGame();};
$('btnAgain').onclick=()=>{S.demo||!S.cam||camReady?startGame():$('btnStart').click();};
$('btnMenu').onclick=()=>{show('sStart');};
refreshChips();
</script>
</body>
</html>
