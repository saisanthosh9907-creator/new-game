<!DOCTYPE html>
<html>
<head>
<meta charset="UTF-8">
<title>Game Hub</title>
<meta name="viewport" content="width=device-width, initial-scale=1.0">

<style>
body{
  margin:0;
  font-family:Arial;
  background:#0f172a;
  color:white;
  text-align:center;
}
header{
  padding:15px;
  font-size:22px;
  background:#020617;
}
button{
  padding:10px 15px;
  margin:5px;
  border:none;
  border-radius:8px;
  font-weight:bold;
}
.home button{width:200px}
.game{display:none}
canvas{
  background:#020617;
  border:2px solid #22c55e;
}
.controls button{
  width:70px;
  height:50px;
}
.health{
  height:15px;
  background:red;
  margin:5px auto;
}
.health span{
  display:block;
  height:100%;
  background:#22c55e;
}
.rank{font-weight:bold;color:gold}
</style>
</head>

<body>

<header>🎮 Game Hub</header>

<!-- HOME -->
<div class="home" id="home">
  <button onclick="openGame('racing')">🏎️ Racing Game</button><br>
  <button onclick="openGame('fight')">🥊 Fighting Game</button>
</div>

<!-- RACING GAME -->
<div id="racing" class="game">
  <button onclick="goHome()">⬅ Back</button>
  <h2>🏎️ Racing</h2>

  <div class="health"><span id="carHealth"></span></div>
  <canvas id="race" width="300" height="450"></canvas>
  <p>Score: <span id="raceScore">0</span></p>
  <p>Rank: <span id="rank" class="rank"></span></p>

  <!-- MOBILE CONTROLS -->
  <div class="controls">
    <button ontouchstart="left=true" ontouchend="left=false">⬅</button>
    <button ontouchstart="right=true" ontouchend="right=false">➡</button>
  </div>
</div>

<!-- FIGHTING GAME -->
<div id="fight" class="game">
  <button onclick="goHome()">⬅ Back</button>
  <h2>🥊 Fighting Game</h2>

  <p>Enemy Health</p>
  <div class="health"><span id="enemyHealth"></span></div>

  <canvas id="fightCanvas" width="300" height="200"></canvas>

  <div class="controls">
    <button onclick="attack()">👊 Punch</button>
    <button onclick="kick()">🦵 Kick</button>
  </div>
</div>

<script>
/* NAV */
function openGame(id){
  home.style.display="none";
  document.querySelectorAll(".game").forEach(g=>g.style.display="none");
  document.getElementById(id).style.display="block";
}
function goHome(){location.reload()}

/* ================= RACING GAME ================= */
const r=document.getElementById("race"),rc=r.getContext("2d");
let car={x:130,y:350},obs=[],score=0,health=100;
let left=false,right=false;
let high=localStorage.getItem("raceHigh")||0;

function rankCalc(s){
  if(s>200) return "Gold 🥇";
  if(s>100) return "Silver 🥈";
  return "Bronze 🥉";
}

setInterval(()=>obs.push({x:Math.random()*260,y:-40}),1200);

setInterval(()=>{
  rc.clearRect(0,0,300,450);

  // car
  rc.fillStyle="lime";
  rc.fillRect(car.x,car.y,30,50);

  // obstacles
  rc.fillStyle="red";
  obs.forEach(o=>{
    o.y+=4;
    rc.fillRect(o.x,o.y,30,50);

    if(o.y>340 && Math.abs(o.x-car.x)<30){
      health-=20;
      o.y=500;
    }
  });

  if(left && car.x>0) car.x-=5;
  if(right && car.x<270) car.x+=5;

  score++;
  raceScore.innerText=score;
  carHealth.style.width=health+"%";
  rank.innerText=rankCalc(score);

  if(health<=0){
    if(score>high) localStorage.setItem("raceHigh",score);
    alert("💥 Crash!\nScore:"+score+"\nHigh Score:"+localStorage.getItem("raceHigh"));
    location.reload();
  }
},30);

document.addEventListener("keydown",e=>{
  if(e.key=="ArrowLeft") left=true;
  if(e.key=="ArrowRight") right=true;
});
document.addEventListener("keyup",()=>{left=false;right=false});

/* ================= FIGHTING GAME ================= */
const fc=document.getElementById("fightCanvas").getContext("2d");
let enemy=100;

function drawFight(){
  fc.clearRect(0,0,300,200);
  fc.fillStyle="blue"; fc.fillRect(40,100,30,60); // player
  fc.fillStyle="red"; fc.fillRect(220,100,30,60); // enemy
  enemyHealth.style.width=enemy+"%";
}
drawFight();

function attack(){
  enemy-=10;
  checkWin();
}
function kick(){
  enemy-=20;
  checkWin();
}
function checkWin(){
  drawFight();
  if(enemy<=0){
    alert("🏆 You Win the Fight!");
    location.reload();
  }
}
</script>

</body>
</html>
